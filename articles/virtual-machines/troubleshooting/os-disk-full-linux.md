---
title: Problemen met een volledige besturingssysteem schijf op een virtuele Linux-machine
description: Problemen oplossen met een volledige besturingssysteem schijf op een virtuele Linux-machine
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523447"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Problemen met een volledige besturingssysteem schijf op een virtuele Linux-machine

Wanneer de besturingssysteem schijf van een virtuele Linux-machine vol raakt, kunnen er problemen ontstaan met de juiste werking van de VM.

## <a name="symptom"></a>Symptoom

Wanneer u een nieuw bestand probeert te maken, wordt dit bericht weer gegeven:

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Meerdere daemons geven vervolgens aan dat ze tijdens de opstart sessie geen tijdelijke bestanden kunnen maken.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Oorzaak

Er zijn verschillende redenen waarom dit fout bericht kan optreden:

1. De schijf is vol.
1. Het bestands systeem heeft mogelijk geen iNodes meer.
1. Een gegevens schijf kan worden gekoppeld via een bestaande map waardoor bestanden worden verborgen.
1. Bestanden die worden geopend door een proces en vervolgens worden verwijderd.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

1. Een herstel-VM maken en openen.
1. Vrije ruimte op schijf.
1. Bouw de virtuele machine opnieuw op.

> [!NOTE]
> Als deze fout optreedt, is het gast besturingssysteem niet operationeel. Los dit probleem op in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
1. Gebruik SSH om verbinding te maken met de reparatie-VM.

### <a name="free-up-space-on-the-disk"></a>Ruimte vrijmaken op de schijf

Om het probleem op te lossen:

- Wijzig de grootte van de schijf tot 1 TB als deze nog niet is ingesteld op de maximum grootte van 1 TB.
- Maak schijf ruimte vrij.

1. Controleer of de schijf vol is. Als de schijf kleiner is dan 1 TB, vouwt u deze uit tot Maxi maal 1 TB [met behulp van Azure cli](../linux/expand-disks.md).
1. Als de schijf al 1 TB is, moet u schijf ruimte vrijmaken.
1. Zodra het formaat is gewijzigd en het opschonen is voltooid, kunt u de virtuele machine opnieuw bouwen.

### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.
