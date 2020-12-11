---
title: Opslagconfiguratie
description: Uitleg over de opslag configuratie opties voor Azure Arc enabled Data Services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 7b683029b7fd05078755d4e8cd027f55c805f991
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107257"
---
# <a name="storage-configuration"></a>Opslagconfiguratie

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Kubernetes opslag concepten

Kubernetes biedt een samen vatting van de infra structuur op basis van de onderliggende technische stack voor virtualisatie (optioneel) en hardware. De manier waarop Kubernetes abstracties opslag ruimte maakt via **[opslag klassen](https://kubernetes.io/docs/concepts/storage/storage-classes/)**. Op het moment van het inrichten van een pod kan een opslag klasse worden opgegeven die voor elk volume moet worden gebruikt. Op het moment dat de Pod wordt ingericht, wordt de opslag klasse- **[inrichting](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** aangeroepen om de opslag in te richten en vervolgens wordt er een **[permanent volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** gemaakt op de ingerichte opslag en vervolgens wordt de pod gekoppeld aan het permanente volume door een claim van een **[permanente volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)**.

Kubernetes biedt een manier om opslag infrastructuur providers in te sluiten in Stuur programma's (ook wel ' addons ' genoemd) die Kubernetes uitbreiden. Invoeg toepassingen voor opslag moeten voldoen aan de **[standaard container Storage-interface](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**. Er zijn tien tallen invoeg toepassingen die u kunt vinden in deze niet-definitieve **[lijst met CSI-Stuur Programma's](https://kubernetes-csi.github.io/docs/drivers.html)**. Welk CSI-stuur programma u gebruikt, is afhankelijk van factoren zoals of u in een door de Cloud gehoste, beheerde Kubernetes-service of de door u gebruikte OEM-provider voor uw hardware werkt.

U kunt weer geven welke opslag klassen zijn geconfigureerd in uw Kubernetes-cluster door de volgende opdracht uit te voeren:

```console
kubectl get storageclass
```

Voor beeld van uitvoer van een Azure Kubernetes service (AKS)-cluster:

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

U kunt details over een opslag klasse verkrijgen door deze opdracht uit te voeren:

```console
kubectl describe storageclass/<storage class name>
```

Voorbeeld:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

U kunt de momenteel ingerichte permanente volumes en claims voor permanente volumes zien door de volgende opdrachten uit te voeren:

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Voor beeld van het weer geven van permanente volumes:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Voor beeld van het weer geven van claims voor permanente volumes:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Factoren waarmee u rekening moet houden bij het kiezen van uw opslag configuratie

Het selecteren van de juiste opslag klasse is belang rijk voor gegevens tolerantie en prestaties. Door de verkeerde opslag klasse te kiezen, kunnen uw gegevens in het geval van een hardwarestoring of het totale verlies van gegevens verloren gaan of kan dit leiden tot minder optimale prestaties.

Er zijn doorgaans twee soorten opslag:

- **Lokale opslag** : opslag ingericht op lokale harde schijven op een bepaald knoop punt. Dit type opslag kan ideaal zijn voor de prestaties, maar moet specifiek worden ontworpen voor gegevens redundantie door de gegevens over meerdere knoop punten te repliceren.
- **Externe, gedeelde opslag** opslag ingericht op sommige externe-opslag apparaten, bijvoorbeeld een San-, NAS-of Cloud opslag service, zoals EBS of Azure files. Dit type opslag biedt over het algemeen automatische gegevens redundantie, maar is niet zo snel als lokale opslag.

> [!NOTE]
> Als u nu gebruikmaakt van NFS, moet u allowRunAsRoot instellen op True in het implementatie profiel bestand voordat u de Azure Arc-gegevens controller implementeert.

### <a name="data-controller-storage-configuration"></a>Opslag configuratie voor gegevens controller

Sommige services in azure Arc voor Data Services zijn afhankelijk van de configuratie van het gebruik van externe gedeelde opslag, omdat de services de gegevens niet kunnen repliceren. Deze services zijn te vinden in de verzameling gegevens controllers van peulen:

|**Service**|**Claims voor permanente volumes**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**SQL-exemplaar van controller**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Controller-API-service**|`<namespace>/data-controller`|

Op het moment dat de gegevens controller wordt ingericht, wordt de opslag klasse die voor elk van deze permanente volumes moet worden gebruikt, opgegeven door de--Storage-klasse door te geven | -SC para meter voor de `azdata arc dc create` opdracht of door de opslag klassen in te stellen in de control.jsop het implementatie sjabloon bestand dat wordt gebruikt.

Voor de implementatie sjablonen die uit het vak worden opgegeven, is een standaard opslag klasse opgegeven die geschikt is voor de doel omgeving, maar deze kan tijdens de implementatie worden overschreven. Zie de gedetailleerde stappen voor het wijzigen van het [implementatie profiel](create-data-controller.md) om de configuratie van de opslag klasse voor de gegevens controller een tijd te geven tijdens de implementatie.

Als u de opslag klasse instelt met behulp van de--opslag klasse | -SC para meter de klasse Storage wordt gebruikt voor logboek-en gegevensopslag klassen. Als u de opslag klassen in het bestand met de implementatie sjabloon instelt, kunt u verschillende opslag klassen voor logboeken en gegevens opgeven.

Belang rijke factoren waarmee u rekening moet houden bij het kiezen van een opslag klasse voor de gegevens controller:

- U **moet** een externe gedeelde opslag klasse gebruiken om de duurzaamheid van de gegevens te waarborgen, zodat als een pod of knoop punt sterft dat wanneer de Pod wordt teruggebracht, verbinding kan maken met het permanente volume.
- De gegevens die worden geschreven naar het SQL-exemplaar van de controller, metrische data BASEs en logboeken DB, zijn doorgaans tamelijk laag volume en niet gevoelig voor latentie, dus Ultra snelle prestatie opslag is niet kritiek. Als u gebruikers hebt die veelvuldig gebruikmaken van de Grafana-en Kibana-interfaces en u beschikt over een groot aantal data base-exemplaren, kunnen uw gebruikers profiteren van snellere opslag.
- De vereiste opslag capaciteit is variabel met het aantal database exemplaren dat u hebt geïmplementeerd, omdat logboeken en metrische gegevens worden verzameld voor elk data base-exemplaar. Gegevens worden gedurende twee (2) weken bewaard in de logboeken en metrische data BASEs voordat deze wordt opgeschoond. 
- Het wijzigen van de post implementatie van de opslag klasse is moeilijk, niet gedocumenteerd, en wordt niet ondersteund. Zorg ervoor dat u de opslag klasse correct kiest tijdens de implementatie.

> [!NOTE]
> Als er geen opslag klasse is opgegeven, wordt de standaard opslag klasse gebruikt. Er kan slechts één standaard opslag klasse per Kubernetes-cluster zijn. U kunt [de standaard opslag klasse wijzigen](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Opslag configuratie voor data base-exemplaren

Elk data base-exemplaar heeft gegevens, logboeken en permanente back-upvolumes. De opslag klassen voor deze permanente volumes kunnen tijdens de implementatie worden opgegeven. Als er geen opslag klasse is opgegeven, wordt de standaard opslag klasse gebruikt.

Wanneer u een exemplaar maakt met ofwel `azdata arc sql mi create` of `azdata arc postgres server create` , zijn er twee para meters die kunnen worden gebruikt voor het instellen van de opslag klassen:

> [!NOTE]
> Sommige van deze para meters zijn in ontwikkeling en worden beschikbaar op `azdata arc sql mi create` en `azdata arc postgres server create` in de komende releases.

|Parameter naam, korte naam|Gebruikt voor|
|---|---|
|`--storage-class-data`, `-scd`|Wordt gebruikt om de opslag klasse op te geven voor alle gegevens bestanden, inclusief transactie logboek bestanden|
|`--storage-class-logs`, `-scl`|Wordt gebruikt om de opslag klasse voor alle logboek bestanden op te geven|
|`--storage-class-data-logs`, `-scdl`|Hiermee geeft u de opslag klasse op voor de transactie logboek bestanden van de data base. **Opmerking: nog niet beschikbaar.**|
|`--storage-class-backups`, `-scb`|Wordt gebruikt om de opslag klasse voor alle back-upbestanden op te geven. **Opmerking: nog niet beschikbaar.**|

In de volgende tabel worden de paden in de Azure SQL Managed instance-container weer gegeven die zijn toegewezen aan het permanente volume voor gegevens en Logboeken:

|Parameter naam, korte naam|Pad in MSSQL-MIAA-container|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Bevat mappen voor de installatie van MSSQL en andere systeem processen. De map MSSQL bevat standaard gegevens (met inbegrip van transactie Logboeken), fouten logboek & back-upmappen|
|`--storage-class-logs`, `-scl`|/var/log|Bevat mappen waarin console-uitvoer (stderr, stdout), andere logboek registratie gegevens van processen binnen de container worden opgeslagen|

De volgende tabel bevat de paden in de PostgreSQL-instantie container die is toegewezen aan het permanente volume voor gegevens en Logboeken:

|Parameter naam, korte naam|Pad in post gres-container|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Bevat gegevens en logboek mappen voor de installatie van post gres|
|`--storage-class-logs`, `-scl`|/var/log|Bevat mappen waarin console-uitvoer (stderr, stdout), andere logboek registratie gegevens van processen binnen de container worden opgeslagen|

Elk data base-exemplaar heeft een afzonderlijk permanent volume voor gegevens bestanden, logboeken en back-ups. Dit betekent dat er een schei ding is tussen de I/O voor elk van deze typen bestanden, afhankelijk van de manier waarop de volume inrichting opslag gaat inrichten. Elk data base-exemplaar heeft een eigen permanente volume claims en permanente volumes.

Als er meerdere data bases op een bepaald data base-exemplaar zijn, gebruiken alle data bases dezelfde permanente volume claim, permanent volume en opslag klasse. Alle back-ups: in differentiële logboek back-ups en volledige back-ups worden dezelfde permanente volume claim en een permanent volume gebruikt. De permanente volume claims voor de data base-instantie peul worden hieronder weer gegeven:

|**Exemplaar**|**Claims voor permanente volumes**|
|---|---|
|**Azure SQL Managed Instance**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure data base for PostgreSQL-exemplaar**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL grootschalige**|`<namespace>/logs-<instance namme>-<ordinal>``<namespace>/data-<instance namme>-<ordinal>` *(Rang telwoord tussen 0 en W waarbij W het aantal werk nemers is)*|

Belang rijke factoren waarmee u rekening moet houden bij het kiezen van een opslag klasse voor het data base-exemplaar:

- Data base-exemplaren kunnen worden geïmplementeerd in één pod patroon of een patroon met meerdere pod. Een voor beeld van één pod-patroon is een ontwikkelaars instantie van een Azure SQL Managed instance of een prijs categorie voor algemeen gebruik Azure SQL Managed instance. Een voor beeld van een pod-patroon is een Maxi maal beschik bare, bedrijfskritische prijs categorie voor Azure SQL Managed instance. (Opmerking: prijs categorieën zijn in ontwikkeling en zijn nog niet beschikbaar voor klanten.)  Data base-exemplaren die zijn geïmplementeerd met het patroon met één Pod, **moeten** een externe gedeelde opslag klasse gebruiken om de duurzaamheid van de gegevens te waarborgen, zodat als een pod of knoop punt sterft dat er een back-up wordt gemaakt op het permanente volume. Een Maxi maal beschik bare Azure SQL Managed instance maakt daarentegen gebruik van AlwaysOn-beschikbaarheids groepen om de gegevens van de ene instantie synchroon of asynchroon te repliceren naar een andere. Met name in het geval dat de gegevens synchroon worden gerepliceerd, zijn er altijd meerdere kopieën van de gegevens, meestal drie (3) kopieën. Daarom is het mogelijk lokale opslag of externe gedeelde opslag klassen te gebruiken voor gegevens en logboek bestanden. Als u lokale opslag gebruikt, blijven de gegevens behouden, zelfs in het geval van een defecte Pod, knoop punt of opslaghardware. Gezien deze flexibiliteit kunt u ervoor kiezen om lokale opslag te gebruiken voor betere prestaties.
- De prestaties van de Data Base zijn grotendeels een functie van de I/O-door Voer van een bepaald opslag apparaat. Als uw data base zware Lees-of zware schrijf bewerkingen is, kiest u een opslag klasse met hardware die is ontworpen voor dat type werk belasting. Als uw data base bijvoorbeeld voornamelijk wordt gebruikt voor schrijf bewerkingen, kunt u lokale opslag kiezen met RAID 0. Als uw data base voornamelijk wordt gebruikt voor lees bewerkingen van een kleine hoeveelheid ' dynamische gegevens ', maar er een groot algemeen opslag volume van koude-gegevens is, kunt u een SAN-apparaat kiezen dat kan worden gelaagd. Het kiezen van de juiste opslag klasse komt niet overeen met het kiezen van het type opslag dat u voor elke Data Base zou gebruiken.
- Als u een lokale opslag volume inrichting gebruikt, moet u ervoor zorgen dat de lokale volumes die zijn ingericht voor gegevens, logboeken en back-ups elk worden geaanvoer op verschillende onderliggende opslag apparaten om conflicten op schijf-I/O te voor komen. Het besturings systeem moet zich ook bevindt op een volume dat is gekoppeld aan een afzonderlijke schijf of schijven. Dit is in wezen dezelfde richt lijnen als voor een Data Base-exemplaar op fysieke hardware.
- Omdat alle data bases op een bepaald exemplaar een claim met een permanente volume en een permanent volume delen, moet u ervoor zorgen dat u geen bezette data base-exemplaren op hetzelfde data base-exemplaar hoeft te verplaatsen. Maak indien mogelijk gebruik van afzonderlijke data bases op hun eigen database instanties om I/O-conflicten te voor komen. U kunt ook knooppunt label doelen gebruiken om data base-exemplaren op afzonderlijke knoop punten te plaatsen, zodat u het totale I/O-verkeer over meerdere knoop punten kunt verdelen. Als u virtualisatie gebruikt, zorg er dan voor dat u I/O-verkeer niet alleen op knooppunt niveau distribueert, maar ook de gecombineerde I/O-activiteit die wordt gebruikt door alle knoop punt-Vm's op een bepaalde fysieke host.

## <a name="estimating-storage-requirements"></a>De opslag vereisten schatten
Elke pod die stateful gegevens bevat, maakt gebruik van twee permanente volumes in deze release: één permanent volume voor gegevens en een ander permanent volume voor Logboeken. In de volgende tabel ziet u het aantal permanente volumes dat is vereist voor één gegevens controller, Azure SQL Managed instance, Azure Database for PostgreSQL-exemplaar en Azure PostgreSQL grootschalige-exemplaar:

|Resourcetype|Aantal stateful peulen|Vereist aantal permanente volumes|
|---|---|---|
|Gegevenscontroller|4 ( `control` , `controldb` , `logsdb` , `metricsdb` )|4 * 2 = 8|
|Azure SQL Managed Instance|1|2|
|Azure Database for PostgreSQL-exemplaar|1| 2|
|Azure PostgreSQL grootschalige|1 + w (W = aantal werk nemers)|2 * (1 + W)|

In de onderstaande tabel ziet u het totale aantal permanente volumes dat is vereist voor een voorbeeld implementatie:

|Resourcetype|Aantal exemplaren|Vereist aantal permanente volumes|
|---|---|---|
|Gegevenscontroller|1|4 * 2 = 8|
|Azure SQL Managed Instance|5|5 * 2 = 10|
|Azure Database for PostgreSQL-exemplaar|5| 5 * 2 = 10|
|Azure PostgreSQL grootschalige|2 (aantal werk nemers = 4 per instantie)|2 * 2 * (1 + 4) = 20|
|***Totaal aantal permanente volumes** _||8 + 10 + 10 + 20 = 48|

Deze berekening kan worden gebruikt voor het plannen van de opslag voor uw Kubernetes-cluster op basis van de opslag inrichting of-omgeving. Als lokale opslag inrichting bijvoorbeeld wordt gebruikt voor een Kubernetes-cluster met vijf (5) knoop punten, moet voor de voorbeeld implementatie hierboven elk knoop punt ten minste opslag voor 10 permanente volumes hebben. En wanneer een Azure Kubernetes service-cluster (AKS) wordt ingericht met vijf (5) knoop punten, wordt een geschikte VM-grootte gekozen voor de knooppunt groep, zodat 10 gegevens schijven kunnen worden bijgevoegd. [Hier](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)vindt u meer informatie over het aanpassen van de grootte van de knoop punten voor opslag behoeften voor AKS-knoop punten.

## <a name="choosing-the-right-storage-class"></a>De juiste opslag klasse kiezen

### <a name="on-premises-and-edge-sites"></a>On-premises en Edge-sites

Micro soft en haar OEM-, OS-en Kubernetes-partners werken aan een certificerings programma voor Azure Arc Data Services. Dit programma biedt klanten vergelijk bare test resultaten van een test Toolkit voor certificering. De tests evalueren de compatibiliteit van functies, stress test resultaten en prestaties en schaal baarheid. Elk van deze test resultaten duidt op het gebruikte besturings systeem, de Kubernetes-distributie, de gebruikte HW, de CSI-invoeg toepassing en de gebruikte opslag klassen. Dit helpt klanten bij het kiezen van de beste opslag klasse, OS, Kubernetes-distributie en HW voor hun vereisten. Meer informatie over dit programma en initiële test resultaten worden dichter bij de algemene Beschik baarheid van Azure Arc Data Services gegeven.

#### <a name="public-cloud-managed-kubernetes-services"></a>Open bare Cloud, beheerde Kubernetes Services

Voor de open bare Cloud beheerde Kubernetes Services kunnen we de volgende aanbevelingen doen:

|Open bare Cloud service|Aanbeveling|
|---|---|
|_ *Azure Kubernetes service (AKS)**|Azure Kubernetes service (AKS) heeft twee typen opslag-Azure Files en Azure Managed Disks. Elk type opslag heeft twee prijs-en prestatie lagen: standaard (HDD) en Premium (SSD). De vier opslag klassen die in AKS zijn opgenomen `azurefile` , zijn (Azure files Standard-laag), ( `azurefile-premium` Azure files Premium-laag), (voor de laag `default` Azure-schijven) en `managed-premium` (Azure disks Premium-laag). De standaard opslag klasse is `default` (de Standard-laag van Azure disks). Er zijn aanzienlijke **[prijs verschillen](https://azure.microsoft.com/en-us/pricing/details/storage/)** tussen de typen en lagen die in uw beslissing moeten worden opgenomen. Voor werk belastingen met hoge prestaties raden we u aan `managed-premium` voor alle opslag klassen te gebruiken. Voor ontwikkel-en test werkbelastingen, het concept van concepten, enzovoort, `azurefile` is de minst dure optie. Alle vier de opties kunnen worden gebruikt voor situaties waarbij externe, gedeelde opslag is vereist, aangezien deze allemaal network-attached storage apparaten in azure zijn. Meer informatie over [AKS-opslag](../../aks/concepts-storage.md).|
|**AWS Elastic Kubernetes Service (EKS)**| De elastische Kubernetes-service van Amazon heeft één primaire opslag klasse, gebaseerd op het [EBS CSI-opslag stuur programma](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). Dit wordt aanbevolen voor productie werkbelastingen. Er is een nieuw opslag stuur programma: [EFS CSI-opslag stuur programma](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) dat kan worden toegevoegd aan een EKS-cluster, maar het is momenteel in een bèta fase en kan worden gewijzigd. Hoewel AWS aangeeft dat dit opslag stuur programma wordt ondersteund voor productie, wordt het niet aanbevolen om het te gebruiken omdat het nog steeds in de bèta versie is en is onderhevig aan wijzigingen. De opslag klasse EBS is de standaard instelling en wordt aangeroepen `gp2` . Meer informatie over [EKS-opslag](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html).|
|**Google Kubernetes Engine (GKE)**|Google Kubernetes Engine (GKE) heeft slechts één opslag klasse met de naam `standard` , die wordt gebruikt voor [GCE permanente schijven](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk). Dit is de enige, het is ook de standaard instelling. Hoewel er een [lokale, statische volume toewijzings](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) functie is voor GKE die u kunt gebruiken met direct gekoppelde ssd's, is het niet raadzaam om deze te gebruiken omdat deze niet wordt onderhouden of wordt ondersteund door Google. Meer informatie over [GKE-opslag](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes).
