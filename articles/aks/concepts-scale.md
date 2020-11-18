---
title: 'Concepten: toepassingen schalen in azure Kubernetes Services (AKS)'
description: Meer informatie over schalen in azure Kubernetes service (AKS), met inbegrip van horizontale pod automatisch schalen, cluster automatisch schalen en de Azure Container Instances-connector.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b72ed7cefc6a16eb484e1337dbd64e5f069a2201
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686035"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Schaalopties voor toepassingen in Azure Kubernetes Service (AKS)

Wanneer u toepassingen uitvoert in azure Kubernetes service (AKS), moet u mogelijk de hoeveelheid reken resources verhogen of verlagen. Als het aantal toepassings exemplaren dat u moet wijzigen, moet het aantal onderliggende Kubernetes-knoop punten mogelijk ook worden gewijzigd. Het is ook mogelijk dat u snel een groot aantal extra toepassings exemplaren moet inrichten.

In dit artikel worden de belangrijkste concepten geïntroduceerd waarmee u toepassingen in AKS kunt schalen:

- [Hand matig schalen](#manually-scale-pods-or-nodes)
- [Pod (HPA)](#horizontal-pod-autoscaler)
- [Automatische schaalaanpassing van clusters](#cluster-autoscaler)
- [Azure container instance (ACI)-integratie met AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Peulen of knoop punten hand matig schalen

U kunt replica's (peul) en knoop punten hand matig schalen om te testen hoe uw toepassing reageert op een wijziging in de beschik bare resources en de status. Door resources hand matig te schalen, kunt u ook een ingestelde hoeveelheid resources definiëren die moeten worden gebruikt voor het onderhouden van vaste kosten, zoals het aantal knoop punten. Als u hand matig wilt schalen, definieert u de replica of het aantal knoop punten. Met de Kubernetes-API wordt vervolgens gepland dat er meer dan een of meer knoop punten worden gemaakt op basis van die replica of het aantal knoop punten.

Bij het omlaag schalen van knoop punten roept de Kubernetes-API de relevante Azure Compute-API aan die is gekoppeld aan het reken type dat door uw cluster wordt gebruikt. Bijvoorbeeld, voor clusters die zijn gebouwd op VM Scale Sets de logica voor het selecteren van de knoop punten die moeten worden verwijderd, wordt bepaald door de VM Scale Sets-API. Zie de [Veelgestelde vragen over VMSS](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed)voor meer informatie over hoe u de geselecteerde knoop punten op schaal kunt verwijderen.

Zie [toepassingen schalen in AKS][aks-scale]om aan de slag te gaan met het hand matig schalen van peulen en knoop punten.

## <a name="horizontal-pod-autoscaler"></a>Horizontale automatische schaalaanpassing van pods

Kubernetes maakt gebruik van de horizontale pod autoscaleer (HPA) voor het bewaken van de vraag van de resource en het automatisch schalen van het aantal replica's. Standaard controleert de pod automatisch schalen de metrische gegevens van de API elke 30 seconden voor eventuele vereiste wijzigingen in het aantal replica's. Als er wijzigingen zijn vereist, wordt het aantal replica's verhoogd of verlaagd. De pod-functie voor Horizon taal automatisch schalen werkt met AKS-clusters die de metrische server voor Kubernetes 1.8 + hebben geïmplementeerd.

![Kubernetes horizon taal pod automatisch schalen](media/concepts-scale/horizontal-pod-autoscaling.png)

Wanneer u de horizontale pod voor een bepaalde implementatie configureert, definieert u het minimale en maximale aantal replica's dat kan worden uitgevoerd. U definieert ook de metriek voor het bewaken en baseren van eventuele beslissingen op, zoals CPU-gebruik.

Zie pod [automatisch schalen in AKS][aks-hpa]om aan de slag te gaan met de horizontale autoscaleer in AKS.

### <a name="cooldown-of-scaling-events"></a>Cooldown van het schalen van gebeurtenissen

Als de horizontale pod autoscaleer de metrische gegevens-API elke 30 seconden controleert, is het mogelijk dat eerdere schaal gebeurtenissen niet zijn voltooid voordat een andere controle is uitgevoerd. Dit gedrag kan ertoe leiden dat de pod van de horizontale automatisch schalen het aantal replica's kan wijzigen voordat de vorige schaal gebeurtenis de werk belasting van de toepassing kan ontvangen en de resource vereisten dienovereenkomstig moet worden aangepast.

Er wordt een vertragings waarde ingesteld om race gebeurtenissen te minimaliseren. Deze waarde bepaalt hoe lang de pod moet wachten na een schaal gebeurtenis voordat een andere schaal gebeurtenis kan worden geactiveerd. Dit gedrag zorgt ervoor dat het nieuwe aantal replica's van kracht wordt en de metrics API om de gedistribueerde werk belasting weer te geven. Er is [geen vertraging voor het opschalen van gebeurtenissen vanaf Kubernetes 1,12](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay), maar de vertraging voor het omlaag schalen van gebeurtenissen wordt standaard ingesteld op 5 minuten.

Op dit moment kunt u deze cooldown-waarden niet afstemmen op de standaard waarde.

## <a name="cluster-autoscaler"></a>Automatische schaalaanpassing van clusters

Om te reageren op het wijzigen van de pod-vraag, heeft Kubernetes een cluster automatisch schalen waarmee het aantal knoop punten wordt aangepast op basis van de aangevraagde Compute-resources in de knooppunt groep. De standaard instelling is dat de API-server van de metriek elke 10 seconden wordt gecontroleerd op vereiste wijzigingen in het aantal knoop punten. Als de automatisch schalen van het cluster bepaalt dat een wijziging vereist is, wordt het aantal knoop punten in uw AKS-cluster verhoogd of verlaagd. De cluster-automatische schaal functie werkt met Kubernetes RBAC-AKS-clusters met Kubernetes 1,10. x of hoger.

![Kubernetes-cluster automatisch schalen](media/concepts-scale/cluster-autoscaler.png)

Cluster automatisch schalen wordt doorgaans samen met de horizontale pod automatisch geschaald gebruikt. In combi natie met de horizontale pod wordt het aantal peulen verg root of verkleind op basis van de vraag van de toepassing en past het cluster automatisch te schalen het aantal knoop punten zo nodig aan om deze te kunnen uitvoeren.

Als u aan de slag wilt gaan met de automatische cluster schaalr in AKS, raadpleegt u [cluster automatisch schalen op AKS][aks-cluster-autoscaler].

### <a name="scale-out-events"></a>Gebeurtenissen uitschalen

Als een knoop punt niet voldoende reken resources heeft om een aangevraagde pod uit te voeren, kan dat pod de voortgang van het plannings proces niet volgen. De pod kan alleen worden gestart als er extra reken bronnen beschikbaar zijn in de knooppunt groep.

Wanneer de cluster-automatische schaal aanpassing het verschil is dat niet kan worden gepland vanwege resource beperkingen van de knooppunt groep, wordt het aantal knoop punten in de knooppunt groep verhoogd om de extra reken bronnen te leveren. Wanneer deze extra knoop punten zijn geïmplementeerd en beschikbaar zijn voor gebruik in de knooppunt groep, wordt het Peul gepland om te worden uitgevoerd.

Als uw toepassing snel moet worden geschaald, kan sommige peulen in een staat wachten om te worden gepland totdat de extra knoop punten die door de cluster-automatische schaal functie worden geïmplementeerd, de geplande peul kunnen accepteren. Voor toepassingen met hoge burst-vereisten kunt u schalen met virtuele knoop punten en Azure Container Instances.

### <a name="scale-in-events"></a>Schalen in gebeurtenissen

De cluster-automatische schaal functie bewaakt ook de pod-plannings status voor knoop punten die recent geen nieuwe plannings aanvragen hebben ontvangen. In dit scenario wordt aangegeven dat de knooppunt groep meer compute-resources heeft dan vereist is en het aantal knoop punten kan worden verkleind.

Een knoop punt dat een drempel waarde door geeft voor niet langer 10 minuten, is standaard gepland voor verwijdering. Wanneer deze situatie zich voordoet, wordt de planning op andere knoop punten in de knooppunt groep uitgevoerd en wordt het aantal knoop punten verkleind door de automatische schaal functie van het cluster.

Het kan voor komen dat uw toepassingen enige onderbrekingen ondervinden wanneer het cluster automatisch schalen het aantal knoop punten verkleint. Vermijd toepassingen die gebruikmaken van één pod-exemplaar om onderbrekingen te minimaliseren.

## <a name="burst-to-azure-container-instances"></a>Burst to Azure Container Instances

Als u uw AKS-cluster snel wilt schalen, kunt u integreren met Azure Container Instances (ACI). Kubernetes heeft ingebouwde onderdelen voor het schalen van de replica en het aantal knoop punten. Als uw toepassing echter snel moet worden geschaald, kan de pod automatisch worden gepland meer dan kan worden verzorgd door de bestaande Compute-resources in de knooppunt groep. Als dit scenario is geconfigureerd, wordt vervolgens de automatische cluster functie geactiveerd om extra knoop punten in de knooppunt groep te implementeren, maar het kan een paar minuten duren voordat deze knoop punten zijn ingericht en de scheduler van de Kubernetes zo groot mogelijk maken.

![Kubernetes burst-schaling naar ACI](media/concepts-scale/burst-scaling.png)

Met ACI kunt u snel container instanties implementeren zonder extra infrastructuur overhead. Wanneer u verbinding maakt met AKS, wordt ACI een beveiligde logische extensie van uw AKS-cluster. Het onderdeel [virtuele knoop punten][virtual-nodes-cli] , dat is gebaseerd op [virtuele-Kubelet][virtual-kubelet], is geïnstalleerd in uw AKS-cluster dat ACI als een virtueel Kubernetes-knoop punt presenteert. Kubernetes kan vervolgens peulen plannen die worden uitgevoerd als ACI-instanties via virtuele knoop punten, en niet als peul op VM-knoop punten direct in uw AKS-cluster.

Uw toepassing hoeft niet te worden gewijzigd om virtuele knoop punten te gebruiken. Implementaties kunnen worden geschaald op basis van AKS en ACI en zonder vertraging als cluster automatisch schalen nieuwe knoop punten implementeert in uw AKS-cluster.

Virtuele knoop punten worden geïmplementeerd naar een extra subnet in hetzelfde virtuele netwerk als uw AKS-cluster. Met deze virtuele netwerk configuratie kan het verkeer tussen ACI en AKS worden beveiligd. Net als een AKS-cluster is een ACI-exemplaar een veilige, logische Compute-resource die is geïsoleerd van andere gebruikers.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met het schalen van toepassingen, volgt u eerst de [Snelstartgids om een AKS-cluster te maken met de Azure cli][aks-quickstart]. U kunt de toepassingen vervolgens hand matig of automatisch schalen in uw AKS-cluster:

- [Peulen][aks-manually-scale-pods] of [knoop punten][aks-manually-scale-nodes] hand matig schalen
- De pod-functie voor [horizontale automatisch schalen][aks-hpa] gebruiken
- De automatische [schaal][aks-cluster-autoscaler] functie van het cluster gebruiken

Raadpleeg de volgende artikelen voor meer informatie over de belangrijkste Kubernetes-en AKS-concepten:

- [Kubernetes/AKS-clusters en-workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-toegang en-identiteit][aks-concepts-identity]
- [Kubernetes/AKS-beveiliging][aks-concepts-security]
- [Kubernetes/AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes/AKS-opslag][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
