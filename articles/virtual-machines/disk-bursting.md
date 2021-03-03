---
title: Bursting van beheerde schijven
description: Meer informatie over schijf bursting voor Azure-schijven en Azure virtual machines.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4024d2b1357f3dda8216e9ebdd2055b28b064d33
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677484"
---
# <a name="managed-disk-bursting"></a>Bursting van beheerde schijven
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Azure [Premium ssd's](disks-types.md#premium-ssd) biedt twee soorten bursting:

- Een bursting-model op aanvraag (preview), waarbij de schijf bursteert wanneer de behoeften de huidige capaciteit overschrijden. Dit model brengt extra kosten in beslag op het moment dat de schijf bursts. Niet-credit bursting is alleen beschikbaar op schijven met een grootte van meer dan 512 GiB.
- Een op Credit gebaseerd model, waarbij de schijf alleen bursteert als deze burst-credits bevat die zijn verzameld in de credit Bucket. In dit model worden geen extra kosten in rekening gebracht wanneer de schijf bursts. Op Credit gebaseerde burstisatie is alleen beschikbaar op schijven 512 GiB en kleiner.

Daarnaast kan de [prestatie-laag van Managed disks worden gewijzigd](disks-change-performance.md). Dit kan ideaal zijn als uw werk belasting anders in burst zou worden uitgevoerd.

|  |Op Credit gebaseerde burstisatie  |Bursting op aanvraag  |Prestatie niveau wijzigen  |
|---------|---------|---------|---------|
| Scenario's|Ideaal voor het schalen op korte termijn (30 minuten of minder).|Ideaal voor kortetermijnbeveiliging (niet beperkt).|Ideaal als uw werk belasting anders voortdurend in burst zou worden uitgevoerd.|
|Kosten     |Gratis         |Kosten zijn variabel, zie de sectie [facturering](#billing) voor meer informatie.        |De kosten van elke laag van de prestaties zijn vast, Zie [Managed disks prijzen](https://azure.microsoft.com/pricing/details/managed-disks/) voor meer informatie.         |
|Beschikbaarheid     |Alleen beschikbaar voor Premium Ssd's 512 GiB en kleiner.         |Alleen beschikbaar voor Premium-Ssd's die groter zijn dan 512 GiB.         |Beschikbaar voor alle Premium SSD-grootten.         |
|Activering     |Standaard ingeschakeld op in aanmerking komende schijven.         |Moet worden ingeschakeld door de gebruiker.         |Gebruikers moeten hun laag hand matig wijzigen.         |

## <a name="common-scenarios"></a>Algemene scenario's
De volgende scenario's kunnen aanzienlijk van bursting profiteren:
- **Opstart tijden verbeteren**  : met bursting wordt uw exemplaar met een aanzienlijk snellere snelheid opgestart. Zo is de standaard besturingssysteem schijf voor Premium ingeschakelde Vm's de P4-schijf. Dit is een ingerichte snelheid van Maxi maal 120 IOPS en 25 MB/s. Met bursting kan de P4 tot 3500 IOPS en 170 MB/s voor opstarten versnellen door tot 6X te gaan.
- **Batch-taken verwerken** : sommige werk belastingen van toepassingen zijn van nature te maken. Ze hebben de meeste tijd basislijn prestaties en hogere prestaties gedurende korte tijd. Een voor beeld hiervan is een boekhoud programma dat dagelijkse trans acties verwerkt waarvoor een kleine hoeveelheid schijf verkeer nodig is. Aan het einde van de maand zou dit programma de reconciliatie rapporten volt ooien die een veel grotere hoeveelheid schijf verkeer nodig hebben.
- **Pieken** in het verkeer: webservers en hun toepassingen kunnen op elk gewenst moment verkeers pieken belasten. Als uw webserver wordt ondersteund door Vm's of schijven die gebruikmaken van bursting, zijn de servers beter uitgerust voor het afhandelen van verkeers pieken. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [op aanvraag bursting inschakelen](disks-enable-bursting.md)om burstisatie op aanvraag in te scha kelen.
Zie [metrische gegevens over schijf bursting](disks-metrics.md)voor meer informatie over het verkrijgen van inzicht in uw burst-resources.
