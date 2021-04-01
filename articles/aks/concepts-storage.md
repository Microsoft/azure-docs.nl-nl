---
title: Concepten-opslag in azure Kubernetes Services (AKS)
description: Meer informatie over opslag in azure Kubernetes service (AKS), inclusief volumes, permanente volumes, opslag klassen en claims
services: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: bf910c66694a62505f259c0a95a88f7dfed05d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127954"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opslag opties voor toepassingen in azure Kubernetes service (AKS)

Toepassingen die worden uitgevoerd in azure Kubernetes service (AKS), moeten mogelijk gegevens opslaan en ophalen. Voor sommige werk belastingen van toepassingen kan deze gegevens opslag gebruikmaken van lokale, snelle opslag op het knoop punt dat niet meer nodig is wanneer het Peul wordt verwijderd. Voor andere werk belastingen van toepassingen is mogelijk opslag vereist die persistent is op meer reguliere gegevens volumes binnen het Azure-platform. Meerdere peulen moeten mogelijk dezelfde gegevens volumes delen of gegevens volumes opnieuw koppelen als de pod op een ander knoop punt opnieuw wordt gepland. Ten slotte moet u mogelijk gevoelige gegevens of informatie over de configuratie van de toepassing in een Peul injecteren.

![Opslag opties voor toepassingen in een AKS-cluster (Azure Kubernetes Services)](media/concepts-storage/aks-storage-options.png)

In dit artikel worden de belangrijkste concepten geïntroduceerd die opslag aan uw toepassingen bieden in AKS:

- [Volumes](#volumes)
- [Permanente volumes](#persistent-volumes)
- [Opslag klassen](#storage-classes)
- [Claims voor permanente volumes](#persistent-volume-claims)

## <a name="volumes"></a>Volumes

Toepassingen moeten vaak gegevens kunnen opslaan en ophalen. Net als Kubernetes behandelt afzonderlijke peulen doorgaans als tijdelijke, wegwerp bronnen, verschillende benaderingen voor het gebruik van toepassingen en het persistent maken van gegevens als dat nodig is. Een *volume* is een manier om gegevens op te slaan, op te halen en te persistent maken en de levens cyclus van de toepassing.

Traditionele volumes voor het opslaan en ophalen van gegevens worden gemaakt als Kubernetes bronnen die door Azure Storage worden ondersteund. U kunt deze gegevens volumes hand matig maken en deze rechtstreeks aan een Peul toewijzen, of Kubernetes automatisch laten maken. Deze gegevens volumes kunnen gebruikmaken van Azure-schijven of Azure Files:

- *Azure-schijven* kunnen worden gebruikt om een Kubernetes *DataDisk* -resource te maken. Schijven kunnen gebruikmaken van Azure Premium Storage, ondersteund door Ssd's met hoge prestaties of Azure Standard-opslag, ondersteund door gewone Hdd's. Voor de meeste werk belastingen voor productie en ontwikkeling gebruikt u Premium Storage. Azure-schijven zijn gekoppeld als *ReadWriteOnce*, dus zijn alleen beschikbaar voor één pod. Gebruik Azure Files voor opslag volumes die gelijktijdig toegankelijk zijn voor meerdere peulen.
- *Azure files* kan worden gebruikt om een SMB 3,0-share die wordt ondersteund door een Azure Storage-account, te koppelen aan het gehele volume. Met bestanden kunt u gegevens delen op meerdere knoop punten en een Peul. Bestanden kunnen Azure Standard-opslag gebruiken die wordt ondersteund door gewone Hdd's of Azure Premium Storage, ondersteund door Ssd's met hoge prestaties.

In Kubernetes kunnen volumes meer vormen dan alleen een traditionele schijf waar informatie kan worden opgeslagen en opgehaald. Kubernetes-volumes kunnen ook worden gebruikt als een manier om gegevens in een pod te injecteren voor gebruik door de containers. Veelvoorkomende aanvullende volume typen in Kubernetes zijn onder andere:

- *emptyDir* : dit volume wordt meestal gebruikt als tijdelijke ruimte voor een pod. Alle containers in een pod hebben toegang tot de gegevens op het volume. Gegevens die naar dit volume type zijn geschreven, blijven behouden voor de levens duur van de pod-wanneer de Pod wordt verwijderd, wordt het volume verwijderd. Dit volume maakt gewoonlijk gebruik van de onderliggende schijf opslag van het lokale knoop punt, maar kan ook alleen bestaan in het geheugen van het knoop punt.
- *geheim* : dit volume wordt gebruikt voor het injecteren van gevoelige gegevens in een Peul, zoals wacht woorden. U maakt eerst een geheim met behulp van de Kubernetes-API. Wanneer u uw Pod of implementatie definieert, kan een specifiek geheim worden aangevraagd. Geheimen worden alleen door gegeven aan knoop punten met een geplande pod waarvoor deze zijn vereist en het geheim wordt opgeslagen in *tmpfs*, niet naar de schijf geschreven. Wanneer de laatste pod op een knoop punt dat een geheim vereist, wordt verwijderd, wordt het geheim verwijderd uit de tmpfs van het knoop punt. Geheimen worden opgeslagen in een opgegeven naam ruimte en kunnen alleen worden gebruikt door een Peul binnen dezelfde naam ruimte.
- *configMap* : dit volume type wordt gebruikt om de eigenschappen van sleutel/waarde-paren in te voeren, zoals informatie over de configuratie van de toepassing. In plaats van de configuratie gegevens van de toepassing te definiëren in een container installatie kopie, kunt u deze definiëren als een Kubernetes-resource die eenvoudig kan worden bijgewerkt en toegepast op nieuwe exemplaren van het Peul wanneer ze worden geïmplementeerd. Net als bij het gebruik van een geheim maakt u eerst een ConfigMap met behulp van de Kubernetes-API. Deze ConfigMap kan vervolgens worden aangevraagd wanneer u een pod of implementatie definieert. ConfigMaps worden opgeslagen in een opgegeven naam ruimte en kunnen alleen worden gebruikt door een Peul binnen dezelfde naam ruimte.

## <a name="persistent-volumes"></a>Permanente volumes

Volumes die worden gedefinieerd en gemaakt als onderdeel van de levens cyclus van de pod bestaan pas nadat de Pod is verwijderd. Het is vaak dat de opslag van een pod op een andere host wordt gepland tijdens een onderhouds gebeurtenis, met name in StatefulSets. Een *permanent volume* (HW) is een opslag resource die wordt gemaakt en beheerd door de KUBERNETES-API en die buiten de levens duur van een afzonderlijke pod kan bestaan.

Azure-schijven of-bestanden worden gebruikt om de PersistentVolume op te geven. Zoals vermeld in de vorige sectie op volumes, wordt de keuze van schijven of bestanden vaak bepaald door de nood zaak van gelijktijdige toegang tot de gegevens of de prestatie-laag.

![Permanente volumes in een Azure Kubernetes Services-cluster (AKS)](media/concepts-storage/persistent-volumes.png)

Een PersistentVolume kan *statisch* worden gemaakt door een cluster beheerder of *dynamisch* gemaakt door de Kubernetes-API-server. Als een pod wordt gepland en er opslag wordt aangevraagd die momenteel niet beschikbaar is, kan Kubernetes de onderliggende Azure-schijf of bestands opslag maken en deze koppelen aan de pod. Dynamische inrichting maakt gebruik van een *StorageClass* om te bepalen welk type Azure-opslag moet worden gemaakt.

## <a name="storage-classes"></a>Opslag klassen

Als u verschillende opslag lagen wilt definiëren, zoals Premium en Standard, kunt u een *StorageClass* maken. De StorageClass definieert ook de *reclaimPolicy*. Deze reclaimPolicy bepaalt het gedrag van de onderliggende Azure Storage-Resource wanneer de Pod wordt verwijderd en het permanente volume mogelijk niet meer nodig is. De onderliggende opslag resource kan worden verwijderd of blijvend worden gebruikt met een toekomstige pod.

In AKS worden vier initialen `StorageClasses` voor het cluster gemaakt met behulp van de modules voor opslag in de boom structuur:

- `default` -Gebruikt Azure StandardSSD-opslag voor het maken van een beheerde schijf. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat deze gebruikt, wordt verwijderd.
- `managed-premium` -Maakt gebruik van Azure Premium Storage voor het maken van een beheerde schijf. Het beleid voor opnieuw claimen zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd.
- `azurefile` -Maakt gebruik van Azure Standard-opslag om een Azure-bestands share te maken. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd.
- `azurefile-premium` -Maakt gebruik van Azure Premium Storage voor het maken van een Azure-bestands share. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd.

Voor clusters die gebruikmaken van de nieuwe container Storage interface (CSI) externe invoeg toepassingen (preview), worden de volgende aanvullende extra `StorageClasses` gemaakt:
- `managed-csi` -Gebruikt Azure StandardSSD lokaal redundante opslag (LRS) voor het maken van een beheerde schijf. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat deze gebruikt, wordt verwijderd. De opslag klasse configureert ook de permanente volumes die kunnen worden uitgebreid. u hoeft alleen de permanente volume claim te bewerken met de nieuwe grootte.
- `managed-csi-premium` -Maakt gebruik van Azure Premium lokaal redundante opslag (LRS) voor het maken van een beheerde schijf. Het beleid voor opnieuw claimen zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd. Op deze opslag klasse kunnen permanente volumes ook worden uitgevouwen.
- `azurefile-csi` -Maakt gebruik van Azure Standard-opslag om een Azure-bestands share te maken. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd.
- `azurefile-csi-premium` -Maakt gebruik van Azure Premium Storage voor het maken van een Azure-bestands share. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd.

Als er geen StorageClass is opgegeven voor een permanent volume, wordt de standaard StorageClass gebruikt. Wees voorzichtig bij het aanvragen van permanente volumes, zodat ze de juiste opslag gebruiken die u nodig hebt. U kunt een StorageClass maken voor extra behoeften met `kubectl` . In het volgende voor beeld wordt Premium Managed Disks gebruikt en wordt aangegeven dat de onderliggende Azure-schijf *behouden moet blijven* wanneer de Pod wordt verwijderd:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS stemt de standaard opslag klassen af en overschrijft alle wijzigingen die u aanbrengt aan deze opslag klassen.

## <a name="persistent-volume-claims"></a>Claims voor permanente volumes

Een PersistentVolumeClaim vraagt schijf-of bestands opslag van een bepaalde StorageClass, toegangs modus en grootte aan. De Kubernetes API-server kan de onderliggende opslag resource in azure dynamisch inrichten als er geen bestaande resource is om te voldoen aan de claim op basis van de gedefinieerde StorageClass. De pod-definitie bevat de volume koppeling zodra het volume is verbonden met de pod.

![Permanente volume claims in een Azure Kubernetes Services-cluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

Een PersistentVolume is *gebonden* aan een PersistentVolumeClaim zodra een beschik bare opslag resource is toegewezen aan de pod die de aanvraag heeft ingediend. Er is een 1:1 toewijzing van permanente volumes aan claims.

In het volgende voor beeld YAML-manifest wordt een permanente volume claim weer gegeven die gebruikmaakt van de *beheerde-Premium* StorageClass en wordt een schijf *5Gi* -grootte aangevraagd:

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

Wanneer u een pod-definitie maakt, wordt de permanente volume claim opgegeven om de gewenste opslag aan te vragen. U geeft ook de *volumeMount* voor uw toepassingen op om gegevens te lezen en te schrijven. In het volgende voor beeld YAML-manifest ziet u hoe de vorige permanente volume claim kan worden gebruikt om een volume te koppelen aan */mnt/Azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Voor het koppelen van een volume in een Windows-container geeft u de stationsletter en het pad op. Bijvoorbeeld:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS][operator-best-practices-storage]voor gekoppelde aanbevolen procedures.

Zie de volgende artikelen met procedures voor informatie over het maken van dynamische en statische volumes die gebruikmaken van Azure-schijven of Azure Files:

- [Een statisch volume maken met behulp van Azure-schijven][aks-static-disks]
- [Een statisch volume maken met behulp van Azure Files][aks-static-files]
- [Een dynamisch volume maken met behulp van Azure-schijven][aks-dynamic-disks]
- [Een dynamisch volume maken met behulp van Azure Files][aks-dynamic-files]

Raadpleeg de volgende artikelen voor meer informatie over de belangrijkste Kubernetes-en AKS-concepten:

- [Kubernetes/AKS-clusters en-workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-identiteit][aks-concepts-identity]
- [Kubernetes/AKS-beveiliging][aks-concepts-security]
- [Kubernetes/AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes/AKS-schaal][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
