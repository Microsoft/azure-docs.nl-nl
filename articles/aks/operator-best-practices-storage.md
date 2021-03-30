---
title: Aanbevolen procedures voor opslag en back-up
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor de cluster operator voor opslag, gegevens versleuteling en back-ups in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 722fe393ad7637be20360463a4c3b6234224a036
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88653967"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service (AKS)

Wanneer u clusters maakt en beheert in azure Kubernetes service (AKS), hebben uw toepassingen vaak opslag nodig. Het is belang rijk om inzicht te krijgen in de prestatie behoeften en toegangs methoden voor een peulheid, zodat u de juiste opslag kunt leveren aan toepassingen. De grootte van het AKS-knoop punt kan van invloed zijn op deze opslag opties. U moet ook plannen voor manieren om een back-up te maken en het herstel proces te testen op gekoppelde opslag.

Deze best practices in dit artikel richt zich op de opslag overwegingen voor cluster operators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Welke typen opslag ruimte beschikbaar zijn
> * De grootte van AKS-knoop punten op de juiste manier aanpassen voor opslag prestaties
> * Verschillen tussen dynamische en statische toewijzing van volumes
> * Manieren om een back-up te maken van uw gegevens volumes en deze te beveiligen

## <a name="choose-the-appropriate-storage-type"></a>Kies het juiste opslag type

**Best Practice-richt lijnen** : inzicht in de behoeften van uw toepassing voor het kiezen van de juiste opslag. Gebruik hoge prestaties, opslag met SSD-back-ups voor productiewerk belastingen. Plan voor netwerk opslag wanneer er meerdere gelijktijdige verbindingen nodig zijn.

Toepassingen vereisen vaak verschillende typen en snelheden van opslag. Zijn er opslag ruimte nodig voor uw toepassingen die worden aangesloten op afzonderlijke peulen of worden gedeeld door meerdere peulen? Is de opslag voor alleen-lezen toegang tot gegevens of om grote hoeveel heden gestructureerde gegevens te schrijven? Deze opslag behoeften bepalen het meest geschikte type opslag dat moet worden gebruikt.

De volgende tabel bevat een overzicht van de beschik bare opslag typen en hun mogelijkheden:

| Gebruiksvoorbeeld | Volume-invoeg toepassing | Eenmaal lezen/schrijven | Alleen-lezen veel | Veel lezen/schrijven | Ondersteuning voor Windows Server-container |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Gedeelde configuratie       | Azure Files   | Ja | Ja | Ja | Ja |
| Gestructureerde app-gegevens        | Azure-schijven   | Ja | Nee  | Nee  | Ja |
| Ongestructureerde gegevens, bestandssysteem bewerkingen | [BlobFuse][blobfuse] | Ja | Ja | Ja | Nee |

De twee primaire typen opslag die voor volumes in AKS worden gegeven, worden ondersteund door Azure disks of Azure Files. Om de beveiliging te verbeteren, gebruiken beide typen opslag standaard SSE (Azure Storage service Encryption) waarmee gegevens in rust worden versleuteld. Schijven kunnen momenteel niet worden versleuteld met Azure Disk Encryption op knooppunt niveau AKS.

Zowel Azure Files als Azure disks zijn beschikbaar in de lagen Standard en Premium.

- *Premium* -schijven worden ondersteund door ssd's (Solid-State Disks) met hoge prestaties. Premium-schijven worden aanbevolen voor alle productie werkbelastingen.
- *Standaard* schijven worden ondersteund door normale spining-schijven (hdd's) en zijn geschikt voor archiverings-of zelden gebruikte gegevens.

Inzicht in de behoeften van de toepassings prestaties en de toegangs patronen om de juiste opslaglaag te kiezen. Zie [overzicht van Azure Managed disks][managed-disks] voor meer informatie over Managed disks grootten en prestatie lagen.

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Opslag klassen maken en gebruiken om toepassings behoeften te definiëren

Het type opslag dat u gebruikt, wordt gedefinieerd met behulp van Kubernetes- *opslag klassen*. Er wordt vervolgens naar de opslag klasse verwezen in de Pod of implementatie specificatie. Deze definities werken samen om de juiste opslag te maken en deze te verbinden met een Peul. Zie [opslag klassen in AKS][aks-concepts-storage-classes]voor meer informatie.

## <a name="size-the-nodes-for-storage-needs"></a>Grootte van de knoop punten voor opslag behoeften

**Richt lijnen voor best practices** : elke knooppunt grootte ondersteunt een maximum aantal schijven. Verschillende knooppunt grootten bieden ook verschillende hoeveel heden lokale opslag en netwerk bandbreedte. Plan uw toepassings vereisten voor het implementeren van de juiste grootte van knoop punten.

AKS-knoop punten worden uitgevoerd als Azure-Vm's. Er zijn verschillende typen en VM-grootten beschikbaar. Elke VM-grootte biedt een andere hoeveelheid kern bronnen, zoals CPU en geheugen. Deze VM-grootten hebben een maximum aantal schijven dat kan worden bijgevoegd. De opslag prestaties zijn ook afhankelijk van de VM-grootten voor de maximale lokale en gekoppelde schijf-IOPS (invoer/uitvoer-bewerkingen per seconde).

Als uw toepassingen Azure-schijven vereisen als opslag oplossing, moet u een geschikte VM-grootte voor het knoop punt plannen en kiezen. De hoeveelheid CPU en geheugen is niet de enige factor wanneer u een VM-grootte kiest. De opslag mogelijkheden zijn ook belang rijk. Zowel de *Standard_B2ms* als *Standard_DS2_v2* VM-grootten bevatten bijvoorbeeld een vergelijk bare hoeveelheid CPU-en geheugen bronnen. De mogelijke opslag prestaties verschillen, zoals wordt weer gegeven in de volgende tabel:

| Type en grootte van knoop punt | vCPU | Geheugen (GiB) | Max. aantal gegevensschijven | Maxi maal aantal IOPS niet in cache geplaatste schijven | Maxi maal aantal door Voer (MBps) niet in cache |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1.920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6.400                  | 96                             |

Hier kan de *Standard_DS2_v2* dubbel het aantal gekoppelde schijven toestaan en drie tot vier keer de hoeveelheid IOPS en schijf doorvoer. Als u alleen de kern Compute-resources en de vergeleken kosten hebt bekeken, kunt u de *Standard_B2ms* VM-grootte kiezen en de prestaties en beperkingen van de opslag verslechteren. Werk samen met uw Application Development-team om inzicht te krijgen in hun opslag capaciteit en prestatie behoeften. Kies de juiste VM-grootte voor de AKS-knoop punten om te voldoen aan de prestatie vereisten of deze te overschrijden. Basislijn toepassingen regel matig om de VM-grootte naar behoefte aan te passen.

Zie [grootten voor virtuele Linux-machines in azure][vm-sizes]voor meer informatie over de beschik bare VM-grootten.

## <a name="dynamically-provision-volumes"></a>Volumes dynamisch inrichten

**Richt lijnen voor best practices** : als u de beheer overhead wilt beperken en u wilt schalen, kunt u geen permanente volumes statisch maken en toewijzen. Dynamische inrichting gebruiken. In uw opslag klassen definieert u het juiste beleid voor het terugclaimen om onnodige opslag kosten te minimaliseren wanneer het Peul wordt verwijderd.

Wanneer u opslag aan een Peul wilt koppelen, gebruikt u permanente volumes. Deze permanente volumes kunnen hand matig of dynamisch worden gemaakt. Als u hand matig permanente volumes maakt, worden er beheer overhead toegevoegd en wordt de mogelijkheid beperkt om de schaal te verg Roten. Gebruik dynamische inrichting van permanente volumes om opslag beheer te vereenvoudigen en uw toepassingen toe te staan om zo nodig te groeien en te schalen.

![Permanente volume claims in een Azure Kubernetes Services-cluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

Met een permanente volume claim (PVC) kunt u zo nodig dynamisch opslag maken. De onderliggende Azure-schijven worden gemaakt als peul aanvragen. In de pod-definitie vraagt u een volume te maken en aan een aangewezen koppelingspad toe te voegen.

Zie voor de concepten over het dynamisch maken en gebruiken van volumes de [claim permanente volumes][aks-concepts-storage-pvcs].

Zie voor het weer geven van deze volumes in actie dynamisch een permanent volume maken en gebruiken met [Azure-schijven][dynamic-disks] of [Azure files][dynamic-files].

Stel, als onderdeel van de definities van uw opslag klassen, de juiste *reclaimPolicy* in. Deze reclaimPolicy bepaalt het gedrag van de onderliggende Azure Storage-Resource wanneer de Pod wordt verwijderd en het permanente volume mogelijk niet meer nodig is. De onderliggende opslag resource kan worden verwijderd of blijvend worden gebruikt met een toekomstige pod. De reclaimPolicy kan worden ingesteld om te worden *behouden* of *verwijderd*. Inzicht in de behoeften van uw toepassing en regel matige controles uitvoeren op opslag die wordt bewaard om de hoeveelheid ongebruikte opslag ruimte te beperken die wordt gebruikt en gefactureerd.

Zie [opslag beleid][reclaim-policy]voor meer informatie over opties voor opslag klassen.

## <a name="secure-and-back-up-your-data"></a>Beveilig en maak een back-up van uw gegevens

**Richt lijnen voor best practices** : Maak een back-up van uw gegevens met behulp van een geschikt hulp programma voor uw opslag type, zoals Velero of Azure backup. Controleer de integriteit en de beveiliging van deze back-ups.

Wanneer uw toepassingen gegevens opslaan en gebruiken die zijn opgeslagen op schijven of in bestanden, moet u regel matig back-ups of moment opnamen van die gegevens maken. Azure-schijven kunnen ingebouwde momentopname technologieën gebruiken. Mogelijk moet u uw toepassingen controleren om schrijf bewerkingen naar de schijf leeg te maken voordat u de momentopname bewerking uitvoert. [Velero][velero] kan back-ups maken van permanente volumes, samen met aanvullende cluster bronnen en-configuraties. Als u de [status van uw toepassingen][remove-state]niet kunt verwijderen, maakt u een back-up van de gegevens van permanente volumes en test u de herstel bewerkingen regel matig om de integriteit van gegevens en de vereiste processen te controleren.

Meer informatie over de beperkingen van de verschillende benaderingen van gegevens back-ups en als u uw gegevens vóór de moment opname moet worden gestileerd. Met gegevens back-ups kunt u de toepassings omgeving van de cluster implementatie niet herstellen. Zie [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in AKS][best-practices-multi-region]voor meer informatie over deze scenario's.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op Aanbevolen procedures voor opslag in AKS. Zie [opslag concepten voor toepassingen in AKS][aks-concepts-storage]voor meer informatie over de basis principes van opslag in Kubernetes.

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
