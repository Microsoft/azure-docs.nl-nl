---
title: Overzicht van back-upopties voor Vm's
description: Overzicht van back-upopties voor Azure virtual machines.
author: cynthn
ms.service: virtual-machines
ms.subservice: recovery
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: c4116ef8d02bd47d6375371be9381553f8c22eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565441"
---
# <a name="backup-and-restore-options-for-virtual-machines-in-azure"></a>Opties voor back-up en herstel voor virtuele machines in azure

U kunt uw gegevens beschermen door regelmatig back-ups te maken. Er zijn verschillende back-upopties beschikbaar voor virtuele machines, afhankelijk van uw gebruiks aanvraag.

## <a name="azure-backup"></a>Azure Backup

Gebruik Azure Backup voor het maken van back-ups van virtuele Azure-machines waarop productie workloads worden uitgevoerd. Azure Backup ondersteunt toepassings consistente back-ups voor virtuele Windows-en Linux-machines. Azure Backup maakt herstelpunten die worden opgeslagen in geografisch redundante Recovery Services-kluizen. Wanneer u vanaf een herstelpunt herstelt, kunt u de hele VM of alleen specifieke bestanden herstellen. 

Zie de [Azure backup Quick](../backup/quick-backup-vm-portal.md)start voor een eenvoudige inleiding tot Azure backup voor virtuele machines van Azure.

Zie [uw VM-back-upinfrastructuur plannen in azure](../backup/backup-azure-vms-introduction.md) voor meer informatie over de werking van Azure backup.


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery beschermt uw Vm's vanuit een belang rijke nood situatie, wanneer een hele regio een storing veroorzaakt door een grote natuur ramp of een uitgebreide service onderbreking. U kunt Azure Site Recovery voor uw virtuele machines configureren, zodat u uw toepassing met één klik in een paar minuten kunt herstellen. U kunt naar een Azure-regio naar keuze repliceren, maar dit is niet beperkt tot gekoppelde regio's. 

U kunt nood herstel oefeningen uitvoeren met testfailover op aanvraag, zonder dat dit van invloed is op uw productie workloads of voortdurende replicatie. Herstel plannen maken om de failover en failback te organiseren van de volledige toepassing die op meerdere Vm's wordt uitgevoerd. De functie herstel plan is geïntegreerd met Azure Automation-runbooks.

U kunt aan de slag door [uw virtuele machines te repliceren](../site-recovery/azure-to-azure-quickstart.md). 

## <a name="managed-snapshots"></a>Beheerde moment opnamen 

In ontwikkel-en test omgevingen bieden moment opnamen een snelle en eenvoudige optie voor het maken van back-ups van virtuele machines die gebruikmaken van Managed Disks. Een beheerde moment opname is een alleen-lezen volledige kopie van een beheerde schijf. Moment opnamen bestaan onafhankelijk van de bron schijf en kunnen worden gebruikt om nieuwe beheerde schijven te maken voor het opnieuw opbouwen van een virtuele machine. Ze worden gefactureerd op basis van het gebruikte gedeelte van de schijf. Als u bijvoorbeeld een moment opname maakt van een beheerde schijf met een ingerichte capaciteit van 64 GB en de daad werkelijke gebruikte gegevens grootte van 10 GB, wordt de moment opname alleen gefactureerd voor de gebruikte gegevens grootte van 10 GB.  

Zie voor meer informatie over het maken van moment opnamen:

* [Een kopie maken van een VHD die is opgeslagen als beheerde schijf met behulp van momentopnamen in Windows](./windows/snapshot-copy-managed-disk.md)
* [Een kopie maken van een VHD die is opgeslagen als beheerde schijf met behulp van momentopnamen in Linux](./linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Volgende stappen
U kunt Azure Backup uitproberen door de [Azure backup Quick](../backup/quick-backup-vm-portal.md)start te volgen.