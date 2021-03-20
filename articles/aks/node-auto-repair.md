---
title: De knoop punten van de Azure Kubernetes-service (AKS) automatisch herstellen
description: Meer informatie over de functionaliteit voor automatisch herstel van knoop punten en hoe AKS de gebroken werk knooppunten herstelt.
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89490102"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatische reparatie van het knoop punt Azure Kubernetes service (AKS)

AKS controleert voortdurend de status van worker-knoop punten en voert automatisch herstel van de knoop punten uit als deze een slechte status hebben. In dit document worden Opera tors geïnformeerd over de werking van automatische knooppunt herstel functionaliteit voor zowel Windows-als Linux-knoop punten. Naast de reparaties van AKS, voert het Azure VM-platform [onderhoud uit op virtual machines][vm-updates] dat ook problemen ondervindt. AKS en Azure-Vm's werken samen om service onderbrekingen voor clusters te minimaliseren.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hoe AKS controleert op beschadigde knoop punten

AKS gebruikt regels om te bepalen of een knoop punt slecht is en moet worden hersteld. AKS maakt gebruik van de volgende regels om te bepalen of automatisch herstel nodig is.

* Het knoop punt rapporteert de status van een **loopvlak** bij opeenvolgende controles binnen een periode van tien minuten
* Het knoop punt rapporteert geen status binnen 10 minuten

U kunt de status van uw knoop punten hand matig controleren met kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Hoe werkt automatisch herstellen?

> [!Note]
> AKS initieert herstel bewerkingen met het gebruikers account **AKS-** herstellen.

Als een knoop punt niet in orde is op basis van de bovenstaande regels en gedurende tien opeenvolgende minuten een slechte status heeft, worden de volgende acties uitgevoerd.

1. Het knoop punt opnieuw opstarten
1. Als de computer niet opnieuw wordt opgestart, moet u de installatie kopie van het knoop punt
1. Als de installatie kopie niet kan worden teruggezet, maakt en vernieuwt u de installatie kopie van het nieuwe knoop punt

Als geen van de acties is geslaagd, worden extra herstel bewerkingen door AKS engineers onderzocht. Als er tijdens een status controle meerdere knoop punten zijn beschadigd, wordt elk knoop punt afzonderlijk hersteld voordat een andere herstel bewerking wordt gestart.

## <a name="next-steps"></a>Volgende stappen

Gebruik [Beschikbaarheidszones][availability-zones] om maximale Beschik baarheid te verg Roten met uw AKS-cluster werkbelastingen.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
