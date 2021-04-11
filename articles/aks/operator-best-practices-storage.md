---
title: Aanbevolen procedures voor opslag en back-up
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor de cluster operator voor opslag, gegevens versleuteling en back-ups in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104912"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service (AKS)

Wanneer u clusters maakt en beheert in azure Kubernetes service (AKS), hebben uw toepassingen vaak opslag nodig. Zorg ervoor dat u inzicht krijgt in de pod-prestatie behoeften en-methoden, zodat u de beste opslag voor uw toepassing kunt selecteren. De grootte van het AKS-knoop punt kan van invloed zijn op uw opslag keuzes. Plan op manieren om een back-up te maken en het herstel proces voor gekoppelde opslag te testen.

Deze best practices in dit artikel richt zich op de opslag overwegingen voor cluster operators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Welke typen opslag ruimte beschikbaar zijn.
> * Hoe u AKS-knoop punten op de juiste manier kunt aanpassen voor opslag prestaties.
> * Verschillen tussen dynamische en statische toewijzing van volumes.
> * Manieren om een back-up te maken van uw gegevens volumes en deze te beveiligen.

## <a name="choose-the-appropriate-storage-type"></a>Kies het juiste opslag type

> **Richtlijnen voor best practices**
> 
> Inzicht in de behoeften van uw toepassing voor het kiezen van de juiste opslag. Gebruik hoge prestaties, opslag met SSD-back-ups voor productiewerk belastingen. Plan voor netwerk opslag wanneer u meerdere gelijktijdige verbindingen nodig hebt.

Toepassingen vereisen vaak verschillende typen en snelheden van opslag. Bepaal het meest geschikte opslag type door de volgende vragen te stellen. 
* Hebben uw toepassingen opslag nodig die verbinding maakt met de afzonderlijke peulen?
* Moeten uw toepassingen opslag ruimte in meerdere peulen delen? 
* Is de opslag voor alleen-lezen toegang tot gegevens?
* Wordt de opslag gebruikt om grote hoeveel heden gestructureerde gegevens te schrijven? 

De volgende tabel bevat een overzicht van de beschik bare opslag typen en hun mogelijkheden:

| Gebruiksvoorbeeld | Volume-invoeg toepassing | Eenmaal lezen/schrijven | Alleen-lezen veel | Veel lezen/schrijven | Ondersteuning voor Windows Server-container |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Gedeelde configuratie       | Azure Files   | Ja | Ja | Ja | Ja |
| Gestructureerde app-gegevens        | Azure-schijven   | Ja | Nee  | Nee  | Ja |
| Ongestructureerde gegevens, bestandssysteem bewerkingen | [BlobFuse][blobfuse] | Ja | Ja | Ja | Nee |

AKS biedt twee belang rijke typen beveiligde opslag voor volumes die worden ondersteund door Azure-schijven of Azure Files. Beide gebruiken standaard Azure Storage service versleuteling (SSE) waarmee gegevens in rust worden versleuteld. Schijven kunnen niet worden versleuteld met Azure Disk Encryption op knooppunt niveau AKS.

Zowel Azure Files als Azure disks zijn beschikbaar in de lagen Standard en Premium.

- *Premium* -schijven
    - Ondersteund door Ssd's (Solid-State Disks) met hoge prestaties. 
    - Aanbevolen voor alle productie werkbelastingen.
- *Standaard* schijven
    - Ondersteund door standaard draaiende schijven (Hdd's).
    - Geschikt voor archiverings-of weinig gebruikte gegevens.

Inzicht in de behoeften van de toepassings prestaties en de toegangs patronen om de juiste opslaglaag te kiezen. Zie [overzicht van Azure Managed disks][managed-disks] voor meer informatie over Managed disks grootten en prestatie lagen.

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Opslag klassen maken en gebruiken om toepassings behoeften te definiëren

Definieer het type opslag dat u wilt maken met behulp van Kubernetes- *opslag klassen*. Er wordt vervolgens naar de opslag klasse verwezen in de Pod of implementatie specificatie. Definities van opslag klassen werken samen om de juiste opslag te maken en deze te verbinden met een Peul. 

Zie [opslag klassen in AKS][aks-concepts-storage-classes]voor meer informatie.

## <a name="size-the-nodes-for-storage-needs"></a>Grootte van de knoop punten voor opslag behoeften

> **Richtlijnen voor best practices**
> 
> Elke knooppunt grootte ondersteunt een maximum aantal schijven. Verschillende knooppunt grootten bieden ook verschillende hoeveel heden lokale opslag en netwerk bandbreedte. Plan de juiste planning voor uw toepassings vereisten voor het implementeren van de juiste grootte van knoop punten.

AKS-knoop punten worden uitgevoerd als verschillende Azure VM-typen en-grootten. Elke VM-grootte biedt:
* Een andere hoeveelheid kern bronnen, zoals CPU en geheugen. 
* Een maximum aantal schijven dat kan worden bijgevoegd. 

De opslag prestaties zijn ook afhankelijk van de VM-grootten voor de maximale lokale en gekoppelde schijf-IOPS (invoer/uitvoer-bewerkingen per seconde).

Als uw toepassingen Azure-schijven vereisen als opslag oplossing, bij de juiste VM-grootte van het knoop punt. De opslag mogelijkheden en CPU-en geheugen aantallen spelen een belang rijke rol bij het bepalen van de grootte van een virtuele machine. 

Als bijvoorbeeld zowel de *Standard_B2ms* als *Standard_DS2_v2* VM-grootten een vergelijk bare hoeveelheid CPU-en geheugen bronnen bevatten, zijn de mogelijke opslag prestaties anders:

| Type en grootte van knoop punt | vCPU | Geheugen (GiB) | Max. aantal gegevensschijven | Maxi maal aantal IOPS niet in cache geplaatste schijven | Maxi maal aantal door Voer (MBps) niet in cache |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1.920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6.400                  | 96                             |

In dit voor beeld bieden de *Standard_DS2_v2* twee keer zoveel gekoppelde schijven en drie tot vier keer de hoeveelheid IOPS en schijf doorvoer. Als u alleen kern Compute-resources en vergeleken kosten vergelijkt, hebt u mogelijk de *Standard_B2ms* VM-grootte gekozen met slechte prestaties en beperkingen voor de opslag. 

Werk samen met uw Application Development-team om inzicht te krijgen in hun opslag capaciteit en prestatie behoeften. Kies de juiste VM-grootte voor de AKS-knoop punten om te voldoen aan de prestatie vereisten of deze te overschrijden. Basislijn toepassingen regel matig om de VM-grootte naar behoefte aan te passen.

Zie [grootten voor virtuele Linux-machines in azure][vm-sizes]voor meer informatie over de beschik bare VM-grootten.

## <a name="dynamically-provision-volumes"></a>Volumes dynamisch inrichten

> **Richtlijnen voor best practices** 
>
> Als u de beheer overhead wilt beperken en schalen wilt inschakelen, moet u permanente volumes statisch maken en toewijzen. Dynamische inrichting gebruiken. In uw opslag klassen definieert u het juiste beleid voor het terugclaimen om onnodige opslag kosten te minimaliseren wanneer het Peul wordt verwijderd.

Gebruik permanente volumes om opslag toe te voegen aan een Peul. Permanente volumes kunnen hand matig of dynamisch worden gemaakt. Door permanente volumes te maken, kunt u hand matig beheer overhead toevoegen en beperkt u de mogelijkheid om te schalen. In plaats daarvan moet u het permanente volume dynamisch inrichten om opslag beheer te vereenvoudigen, zodat uw toepassingen zo nodig kunnen worden uitgebreid en geschaald.

![Permanente volume claims in een Azure Kubernetes Services-cluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

Met een permanente volume claim (PVC) kunt u zo nodig dynamisch opslag maken. De onderliggende Azure-schijven worden gemaakt als een Peul aanvraag. Vraag in de pod-definitie een volume aan dat moet worden gemaakt en gekoppeld aan een aangewezen koppelingspad.

Zie voor de concepten over het dynamisch maken en gebruiken van volumes de [claim permanente volumes][aks-concepts-storage-pvcs].

Zie voor het weer geven van deze volumes in actie dynamisch een permanent volume maken en gebruiken met [Azure-schijven][dynamic-disks] of [Azure files][dynamic-files].

Stel, als onderdeel van de definities van uw opslag klassen, de juiste *reclaimPolicy* in. Deze reclaimPolicy bepaalt het gedrag van de onderliggende Azure Storage-Resource wanneer de Pod wordt verwijderd. De onderliggende opslag resource kan worden verwijderd of bewaard voor toekomstig pod gebruik. Stel de reclaimPolicy in op *behouden* of *verwijderen*. 

Inzicht in de behoeften van uw toepassing en regel matige controles uitvoeren op behouden opslag om de hoeveelheid ongebruikte en gefactureerde opslag ruimte te minimaliseren.

Zie [opslag beleid][reclaim-policy]voor meer informatie over opties voor opslag klassen.

## <a name="secure-and-back-up-your-data"></a>Beveilig en maak een back-up van uw gegevens

> **Richtlijnen voor best practices** 
> 
> Maak een back-up van uw gegevens met behulp van een geschikt hulp programma voor uw opslag type, zoals Velero of Azure Backup. Controleer de integriteit en beveiliging van deze back-ups.

Wanneer uw toepassingen gegevens opslaan en gebruiken die zijn opgeslagen op schijven of in bestanden, moet u regel matig back-ups of moment opnamen van die gegevens maken. Azure-schijven kunnen ingebouwde momentopname technologieën gebruiken. Uw toepassingen moeten mogelijk schrijf bewerkingen naar de schijf leegmaken voordat u de momentopname bewerking uitvoert. [Velero][velero] kan back-ups maken van permanente volumes, samen met aanvullende cluster bronnen en-configuraties. Als u de [status van uw toepassingen][remove-state]niet kunt verwijderen, maakt u een back-up van de gegevens van permanente volumes en test u de herstel bewerkingen regel matig om de integriteit van gegevens en de vereiste processen te controleren.

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
