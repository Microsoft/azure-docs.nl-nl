---
title: De knoop punten van de Azure Kubernetes-service (AKS) automatisch herstellen
description: Meer informatie over de functionaliteit voor automatisch herstel van knoop punten en hoe AKS de gebroken werk knooppunten herstelt.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105201"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatische reparatie van het knoop punt Azure Kubernetes service (AKS)

AKS bewaakt voortdurend de status van worker-knoop punten en voert automatisch een herstel van het knoop punt uit als ze een slechte status hebben. Het Azure virtual machine (VM)-platform [voert onderhoud uit op vm's][vm-updates] die problemen ondervinden. 

AKS en Azure-Vm's werken samen om service onderbrekingen voor clusters te minimaliseren.

In dit document leert u hoe automatische functionaliteit voor knooppunt herstel zich gedraagt voor zowel Windows-als Linux-knoop punten. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hoe AKS controleert op beschadigde knoop punten

AKS maakt gebruik van de volgende regels om te bepalen of een knoop punt een slechte status heeft en moet worden hersteld: 
* Het knoop punt **rapporteert** de status van de niet-opeenvolgende controles binnen een periode van tien minuten.
* Het knoop punt rapporteert geen status binnen 10 minuten.

U kunt de status van uw knoop punten hand matig controleren met kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Hoe werkt automatisch herstellen?

> [!Note]
> AKS initieert herstel bewerkingen met het gebruikers account **AKS-** herstellen.

Als AKS een niet-gezond knoop punt identificeert dat gedurende tien minuten niet in orde is, neemt AKS de volgende acties:

1. Start het knoop punt opnieuw op.
1. Als het opnieuw opstarten is mislukt, moet u de installatie kopie van het knoop punt opnieuw.
1. Als de installatie kopie niet kan worden teruggezet, maakt en vernieuwt u een nieuwe installatie kopie van het knoop punt.

Alternatieve herstel bewerkingen worden door AKS engineers onderzocht als het automatisch herstellen is mislukt. 

Als AKS meerdere beschadigde knoop punten vindt tijdens een status controle, wordt elk knoop punt afzonderlijk gerepareerd voordat een ander herstel wordt gestart.

## <a name="next-steps"></a>Volgende stappen

Gebruik [Beschikbaarheidszones][availability-zones] om maximale Beschik baarheid te verg Roten met uw AKS-cluster werkbelastingen.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
