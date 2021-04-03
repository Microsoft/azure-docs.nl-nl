---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518050"
---
Dit artikel beschrijft de prestaties van de schijf en hoe deze werkt wanneer u Azure Virtual Machines en Azure-schijven combineert. Ook wordt beschreven hoe u knel punten kunt vaststellen voor uw schijf-i/o en de wijzigingen die u kunt aanbrengen om de prestaties te optimaliseren.

## <a name="how-does-disk-performance-work"></a>Hoe werkt de prestaties van de schijf?
Virtuele Azure-machines hebben invoer/uitvoer-bewerkingen per seconde (IOPS) en limieten voor doorvoer prestaties op basis van het type en de grootte van de virtuele machine. Schijven van het besturings systeem en gegevens schijven kunnen worden gekoppeld aan virtuele machines. De schijven hebben hun eigen IOPS en doorvoer limieten.

De prestaties van uw toepassing worden afgetoptd wanneer er meer IOPS of door Voer wordt aangevraagd dan wat is toegewezen aan de virtuele machines of gekoppelde schijven. Als de app wordt beperkt, ondervindt de toepassing de beste prestaties. Dit kan leiden tot negatieve gevolgen als verhoogde latentie. Laten we een paar voor beelden gebruiken om dit concept te verduidelijken. Om deze voor beelden gemakkelijk te kunnen volgen, kijken we alleen naar IOPS. Maar dezelfde logica is van toepassing op door voer.

## <a name="disk-io-capping"></a>Uitgetopte schijf-i/o

**Sample**

- Standard_D8s_v3
  - IOPS niet in cache: 12.800
- E30-besturingssysteem schijf
  - IOPS: 500
- Twee E30-gegevens schijven × 2
  - IOPS: 500

![Diagram van het afgetopten op schijf niveau.](media/vm-disk-performance/disk-level-throttling.jpg)

De toepassing die op de virtuele machine wordt uitgevoerd, maakt een aanvraag waarbij 10.000 IOPS voor de virtuele machine is vereist. Deze zijn allemaal toegestaan door de VM, omdat de Standard_D8s_v3 virtuele machine Maxi maal 12.800 IOPS kan uitvoeren.

De 10.000 IOPS-aanvragen worden opgesplitst in drie verschillende aanvragen naar de verschillende schijven:

- 1.000 IOPS wordt gevraagd naar de schijf met het besturings systeem.
- 4.500 IOPS is aangevraagd voor elke gegevens schijf.

Alle bijgevoegde schijven zijn E30 schijven en kunnen alleen 500 IOPS afhandelen. Dus reageren ze op elk van 500 IOPS. De prestaties van de toepassing worden beperkt door de gekoppelde schijven en kunnen alleen 1.500 IOPS verwerken. De toepassing kan bij piek prestaties op 10.000 IOPS werken als er betere schijven worden gebruikt, zoals Premium-SSD P30-schijven.

## <a name="virtual-machine-io-capping"></a>Uitgetopte IO van virtuele machine

**Sample**

- Standard_D8s_v3
  - IOPS niet in cache: 12.800
- P30-besturingssysteem schijf
  - IOPS: 5.000
- Twee P30-gegevens schijven × 2
  - IOPS: 5.000

![Diagram van het niveau van de afgetopting van virtuele machines.](media/vm-disk-performance/vm-level-throttling.jpg)

De toepassing die op de virtuele machine wordt uitgevoerd, maakt een aanvraag waarvoor 15.000 IOPS is vereist. Helaas wordt de virtuele machine van Standard_D8s_v3 alleen ingericht voor de verwerking van 12.800 IOPS. De toepassing wordt beperkt door de limieten van de virtuele machine en moet de toegewezen 12.800 IOPS toewijzen.

De aangevraagde 12.800-IOPS worden opgesplitst in drie verschillende aanvragen naar de verschillende schijven:

- 4.267 IOPS wordt gevraagd naar de schijf met het besturings systeem.
- 4.266 IOPS is aangevraagd voor elke gegevens schijf.

Alle bijgevoegde schijven zijn P30 schijven die 5.000 IOPS kunnen verwerken. Dus reageren ze op basis van de aangevraagde bedragen.
