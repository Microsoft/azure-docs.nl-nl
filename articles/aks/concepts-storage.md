---
title: Concepten-opslag in azure Kubernetes Services (AKS)
description: Meer informatie over opslag in azure Kubernetes service (AKS), inclusief volumes, permanente volumes, opslag klassen en claims
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105779"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opslag opties voor toepassingen in azure Kubernetes service (AKS)

Toepassingen die worden uitgevoerd in azure Kubernetes service (AKS), moeten mogelijk gegevens opslaan en ophalen. Hoewel sommige werk belastingen van toepassingen gebruik kunnen maken van lokale, snelle opslag op overbodige, geleegde knoop punten, hebben anderen opslag nodig die persistent is op meer reguliere gegevens volumes binnen het Azure-platform. 

Meerdere peulen moeten mogelijk:
* Deel dezelfde gegevens volumes. 
* Koppel gegevens volumes opnieuw als de pod opnieuw wordt gepland op een ander knoop punt. 

Ten slotte moet u mogelijk gevoelige gegevens of informatie over de configuratie van de toepassing in een Peul injecteren.

In dit artikel worden de belangrijkste concepten geïntroduceerd die opslag aan uw toepassingen bieden in AKS:

- [Volumes](#volumes)
- [Permanente volumes](#persistent-volumes)
- [Opslag klassen](#storage-classes)
- [Claims voor permanente volumes](#persistent-volume-claims)

![Opslag opties voor toepassingen in een AKS-cluster (Azure Kubernetes Services)](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Volumes

Kubernetes behandelt de afzonderlijke peulen doorgaans als tijdelijke, wegwerp bronnen. Er zijn verschillende benaderingen beschikbaar voor toepassingen voor het gebruik en het persistent maken van gegevens. Een *volume* is een manier om gegevens op te slaan, op te halen en te persistent maken en de levens cyclus van de toepassing.

Traditionele volumes worden gemaakt als Kubernetes-bronnen die door Azure Storage worden ondersteund. U kunt hand matig gegevens volumes maken die rechtstreeks aan een Peul moeten worden toegewezen, of ze hebben Kubernetes automatisch gemaakt. Gegevens volumes kunnen gebruikmaken van Azure-schijven of-Azure Files.

### <a name="azure-disks"></a>Azure-schijven

Gebruik *Azure disks* om een Kubernetes *DataDisk* -resource te maken. Schijven kunnen gebruiken:
* Azure Premium-opslag, ondersteund door Ssd's met hoge prestaties, of 
* Azure Standard-opslag, ondersteund door gewone Hdd's. 

> [!TIP]
>Voor de meeste werk belastingen voor productie en ontwikkeling gebruikt u Premium Storage. 

Omdat Azure-schijven zijn gekoppeld als *ReadWriteOnce*, zijn ze alleen beschikbaar voor één pod. Gebruik Azure Files voor opslag volumes die gelijktijdig toegankelijk zijn voor meerdere peulen.

### <a name="azure-files"></a>Azure Files
Gebruik *Azure files* om een SMB 3,0-share die wordt ondersteund door een Azure Storage-account, te koppelen aan het gehele volume. Met bestanden kunt u gegevens delen op meerdere knoop punten en een Peul en gebruiken:
* Azure Premium-opslag, ondersteund door Ssd's met hoge prestaties, of 
* Azure Standard-opslag wordt ondersteund door reguliere Hdd's.

### <a name="volume-types"></a>Volume typen
Kubernetes-volumes vertegenwoordigen meer dan alleen een traditionele schijf voor het opslaan en ophalen van informatie. Kubernetes-volumes kunnen ook worden gebruikt als een manier om gegevens in een pod te injecteren voor gebruik door de containers. 

Algemene volume typen in Kubernetes zijn:

#### <a name="emptydir"></a>emptyDir

Wordt vaak gebruikt als tijdelijke ruimte voor een pod. Alle containers in een pod hebben toegang tot de gegevens op het volume. Gegevens die naar dit volume type zijn geschreven, blijven behouden voor de levens duur van de pod. Wanneer u de pod verwijdert, wordt het volume verwijderd. Dit volume maakt gewoonlijk gebruik van de onderliggende schijf opslag van het lokale knoop punt, maar kan ook alleen bestaan in het geheugen van het knoop punt.

#### <a name="secret"></a>geheim

U kunt *geheime* volumes gebruiken om gevoelige gegevens in te voeren, zoals wacht woorden. 
1. Maak een geheim met behulp van de Kubernetes-API. 
1. Definieer uw Pod of implementatie en vraag een specifiek geheim aan. 
    * Geheimen worden alleen door gegeven aan knoop punten met een geplande pod waarvoor ze nodig zijn.
    * Het geheim wordt opgeslagen in *tmpfs*, niet naar schijf geschreven. 
1. Wanneer u de laatste pod verwijdert op een knoop punt dat een geheim vereist, wordt het geheim verwijderd uit de tmpfs van het knoop punt. 
   * Geheimen worden opgeslagen in een opgegeven naam ruimte en kunnen alleen worden gebruikt door een Peul binnen dezelfde naam ruimte.

#### <a name="configmap"></a>configMap

U kunt *configMap* gebruiken om de eigenschappen van sleutel/waarde-paren in te voeren, zoals informatie over de configuratie van de toepassing. Definieer de configuratie gegevens van de toepassing als een Kubernetes-resource, die eenvoudig kan worden bijgewerkt en toegepast op nieuwe exemplaren van een Peul tijdens de implementatie. 

Net als bij het gebruik van een geheim:
1. Maak een ConfigMap met behulp van de Kubernetes-API. 
1. Vraag de ConfigMap aan wanneer u een pod of implementatie definieert. 
   * ConfigMaps worden opgeslagen in een opgegeven naam ruimte en kunnen alleen worden gebruikt door een Peul binnen dezelfde naam ruimte.

## <a name="persistent-volumes"></a>Permanente volumes

Volumes die zijn gedefinieerd en gemaakt als onderdeel van de levens cyclus van de pod bestaan pas nadat u de pod verwijdert. Het is vaak dat de opslag van een pod op een andere host wordt gepland tijdens een onderhouds gebeurtenis, met name in StatefulSets. Een *permanent volume* (HW) is een opslag resource die wordt gemaakt en beheerd door de KUBERNETES-API en die buiten de levens duur van een afzonderlijke pod kan bestaan.

U kunt Azure-schijven of-bestanden gebruiken om de PersistentVolume te bieden. Zoals vermeld in de sectie [volumes](#volumes) , wordt de keuze van schijven of bestanden vaak bepaald door de nood zaak van gelijktijdige toegang tot de gegevens of de prestatie-laag.

![Permanente volumes in een Azure Kubernetes Services-cluster (AKS)](media/concepts-storage/persistent-volumes.png)

Een PersistentVolume kan *statisch* worden gemaakt door een cluster beheerder of *dynamisch* gemaakt door de Kubernetes-API-server. Als een pod wordt gepland en momenteel niet-beschik bare opslag aanvragen, kan Kubernetes de onderliggende Azure-schijf of bestands opslag maken en deze koppelen aan de pod. Dynamische inrichting maakt gebruik van een *StorageClass* om te bepalen welk type Azure-opslag moet worden gemaakt.

## <a name="storage-classes"></a>Opslag klassen

Als u verschillende opslag lagen wilt definiëren, zoals Premium en Standard, kunt u een *StorageClass* maken. 

De StorageClass definieert ook de *reclaimPolicy*. Wanneer u de pod verwijdert en het permanente volume niet meer vereist is, bepaalt de reclaimPolicy het gedrag van de onderliggende Azure Storage-Resource. De onderliggende opslag resource kan worden verwijderd of bewaard voor gebruik met een toekomstige pod.

In AKS worden vier initialen `StorageClasses` voor het cluster gemaakt met behulp van de modules voor opslag in de boom structuur:

| Machtiging | Reden |
|---|---|
| `default` | Maakt gebruik van Azure StandardSSD-opslag voor het maken van een beheerde schijf. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat deze gebruikt, wordt verwijderd. |
| `managed-premium` | Maakt gebruik van Azure Premium Storage voor het maken van een beheerde schijf. Het beleid voor opnieuw claimen zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd. |
| `azurefile` | Maakt gebruik van Azure Standard-opslag om een Azure-bestands share te maken. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd. |
| `azurefile-premium` | Maakt gebruik van Azure Premium Storage voor het maken van een Azure-bestands share. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd.|

Voor clusters die gebruikmaken van de nieuwe container Storage interface (CSI) externe invoeg toepassingen (preview), worden de volgende extra `StorageClasses` Opties gemaakt:

| Machtiging | Reden |
|---|---|
| `managed-csi` | Maakt gebruik van Azure StandardSSD lokaal redundante opslag (LRS) voor het maken van een beheerde schijf. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat deze gebruikt, wordt verwijderd. De opslag klasse configureert ook de permanente volumes die kunnen worden uitgebreid. u hoeft alleen de permanente volume claim te bewerken met de nieuwe grootte. |
| `managed-csi-premium` | Maakt gebruik van Azure Premium lokaal redundante opslag (LRS) voor het maken van een beheerde schijf. Het beleid voor opnieuw claimen zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd. Op deze opslag klasse kunnen permanente volumes ook worden uitgevouwen. |
| `azurefile-csi` | Maakt gebruik van Azure Standard-opslag om een Azure-bestands share te maken. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd. |
| `azurefile-csi-premium` | Maakt gebruik van Azure Premium Storage voor het maken van een Azure-bestands share. Het beleid voor opnieuw claim zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het permanente volume dat wordt gebruikt, wordt verwijderd.|

Tenzij u een StorageClass voor een permanent volume opgeeft, wordt de standaard StorageClass gebruikt. Zorg ervoor dat volumes de juiste opslag gebruiken die u nodig hebt bij het aanvragen van permanente volumes. 

U kunt een StorageClass maken voor extra behoeften met `kubectl` . In het volgende voor beeld wordt Premium Managed Disks gebruikt en wordt aangegeven dat de onderliggende Azure-schijf *behouden moet blijven* wanneer u de pod verwijdert:

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

Een PersistentVolumeClaim vraagt schijf-of bestands opslag van een bepaalde StorageClass, toegangs modus en grootte aan. De Kubernetes API-server kan de onderliggende Azure Storage-Resource dynamisch inrichten als er geen bestaande resource kan voldoen aan de claim op basis van de gedefinieerde StorageClass. 

De pod-definitie bevat de volume koppeling zodra het volume is verbonden met de pod.

![Permanente volume claims in een Azure Kubernetes Services-cluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

Zodra een beschik bare opslag resource is toegewezen aan de pod die de opslag heeft aangevraagd, is PersistentVolume *gebonden* aan een PersistentVolumeClaim. Permanente volumes zijn 1:1 toegewezen aan claims.

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

Wanneer u een pod-definitie maakt, moet u ook het volgende opgeven:
* De permanente volume claim om de gewenste opslag aan te vragen. 
* De *volumeMount* voor uw toepassingen om gegevens te lezen en te schrijven. 

In het volgende voor beeld YAML-manifest ziet u hoe de vorige permanente volume claim kan worden gebruikt om een volume te koppelen aan */mnt/Azure*:

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
