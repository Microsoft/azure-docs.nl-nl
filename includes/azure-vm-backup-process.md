---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716739"
---
1. Voor Azure-VM's die zijn geselecteerd voor back-up, Azure Backup een back-up job volgens het back-upschema dat u opgeeft.
1. Tijdens de eerste back-up wordt een back-upextensie geïnstalleerd op de VM als de VM wordt uitgevoerd.
    - Voor Windows-VM's wordt [de extensie VMSnapshot](../articles/virtual-machines/extensions/vmsnapshot-windows.md) geïnstalleerd.
    - Voor Linux-VM's wordt [de extensie VMSnapshotLinux](../articles/virtual-machines/extensions/vmsnapshot-linux.md) geïnstalleerd.
1. Voor Windows-VM's die worden uitgevoerd, coördineert Backup met Windows Volume Shadow Copy Service (VSS) om een app-consistente momentopname van de VM te maken.
    - Back-up maakt standaard volledige VSS-back-ups.
    - Als Back-up geen app-consistente momentopname kan maken, wordt er een bestands-consistente momentopname van de onderliggende opslag gemaakt (omdat er geen schrijf schrijven van de toepassing plaatsvindt terwijl de VM is gestopt).
1. Voor Linux-VM's maakt Back-up een bestands-consistente back-up. Voor app-consistente momentopnamen moet u scripts vooraf/achteraf handmatig aanpassen.
1. Nadat Backup de momentopname heeft gemaakt, worden de gegevens naar de kluis overgeboekt.
    - De back-up wordt geoptimaliseerd door parallel een back-up te maken van elke VM-schijf.
    - Azure Backup leest de blokken op elke schijf waarvan een back-up wordt gemaakt, en identificeert en verplaatst alleen de gegevensblokken die sinds de vorige back-up zijn gewijzigd (de delta).
    - Momentopnamegegevens worden mogelijk niet direct naar de kluis gekopieerd. Het kan enkele uren duren tijdens piekmomenten. De totale back-uptijd voor een virtuele machine is minder dan 24 uur bij beleid voor dagelijkse back-ups.
1. Wijzigingen die zijn aangebracht in een Windows-VM nadat Azure Backup is ingeschakeld, zijn:
    - Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 is geïnstalleerd in de VM
    - Het opstarttype van de Volume Shadow Copy-service (VSS) is gewijzigd in automatisch van handmatig
    - De Windows-service IaaSVmProvider is toegevoegd

1. Wanneer de gegevensoverdracht is voltooid, wordt de momentopname verwijderd en wordt er een herstelpunt gemaakt.

![Back-uparchitectuur voor virtuele Azure-machines](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
