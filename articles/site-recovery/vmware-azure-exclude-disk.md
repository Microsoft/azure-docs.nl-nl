---
title: VMware-VM-schijven uitsluiten van herstel na nood geval naar Azure met Azure Site Recovery
description: VMware VM-schijven uitsluiten van replicatie naar Azure met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129880"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Schijven uitsluiten van replicatie van virtuele VMware-machines naar Azure

In dit artikel wordt beschreven hoe u schijven uitsluit bij het repliceren van virtuele VMware-machines naar Azure voor herstel na nood gevallen. Mogelijk wilt u schijven uitsluiten van replicatie om een aantal redenen:

- Zorg ervoor dat niet-belang rijke gegevens die op de uitgesloten schijf worden overgevoerd, niet worden gerepliceerd.
- Optimaliseer de verbruikte replicatie bandbreedte of de bronnen aan de doel zijde door de schijven uit te sluiten die u niet hoeft te repliceren.
- Sla opslag-en netwerk bronnen op door geen gegevens te repliceren die u niet nodig hebt.

Voordat u schijven uitsluit van replicatie:

- Meer [informatie](exclude-disks-replication.md) over het uitsluiten van schijven.
- Bekijk [typische uitsluitings scenario's](exclude-disks-replication.md#typical-scenarios) en [voor beelden](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) die laten zien hoe de replicatie, failover en failback wordt beïnvloed door een schijf.

## <a name="before-you-start"></a>Voordat u begint

 Let op het volgende voordat u begint:

- **Replicatie**: standaard worden alle schijven op een machine gerepliceerd.
- **Schijf type**: alleen basis schijven kunnen worden uitgesloten van replicatie. Besturingssystemen en dynamische schijven kunt u niet uitsluiten.
- **Mobility-service**: als u een schijf wilt uitsluiten van replicatie, moet u de Mobility-service hand matig installeren op de machine voordat u replicatie inschakelt. U kunt de push-installatie niet gebruiken omdat deze methode de Mobility-service alleen op een VM installeert nadat de replicatie is ingeschakeld.  
- **Schijven toevoegen/verwijderen/uitsluiten**: wanneer u replicatie inschakelt, kunt u geen schijven toevoegen/verwijderen of uitsluiten voor replicatie. Als u schijven wilt toevoegen/verwijderen of uitsluiten, moet u de beveiliging voor de machine uitschakelen en vervolgens weer inschakelen.
- **Failover**: na een failover, moet u deze schijven hand matig maken als er een fout is opgetreden bij het uitvoeren van apps die zijn uitgesloten van een app. U kunt Azure Automation ook integreren in een herstel plan om de schijf te maken tijdens de failover van de machine.
- **Failback-Windows**: wanneer u na een failover een failback naar uw on-premises site hebt uitgevoerd, worden Windows-schijven die u hand matig hebt gemaakt in azure niet meer weer gegeven. Als u bijvoorbeeld een failover hebt uitgevoerd voor drie schijven en twee schijven rechtstreeks op virtuele Azure-machines maakt, worden alleen de drie schijven waarvoor een failover is voltooid, hersteld.
- **Failback-Linux**: voor het failback van Linux-machines worden schijven die u hand matig in azure maakt, teruggezet. Als u bijvoorbeeld een failover hebt uitgevoerd voor drie schijven en twee schijven rechtstreeks op virtuele Azure-machines maakt, worden er geen vijf back-ups meer gemaakt. U kunt geen schijven uitsluiten die hand matig zijn gemaakt in de failback of bij het opnieuw beveiligen van Vm's.



## <a name="exclude-disks-from-replication"></a>Schijven uitsluiten van replicatie

1. Wanneer u [replicatie inschakelt](./hyper-v-azure-tutorial.md) voor een VMware-VM, moet u na het selecteren van de virtuele machines die u wilt repliceren, op de pagina eigenschappen van **replicatie inschakelen**  >    >  **configureren** de kolom **te repliceren schijven** controleren. Standaard zijn alle schijven geselecteerd voor replicatie.
2. Als u een specifieke schijf niet wilt repliceren, wist u de selectie van de schijven die u wilt uitsluiten als u de schijven wilt **repliceren** . 

    ![Schijven uitsluiten van replicatie](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Volgende stappen
Wanneer uw implementatie actief is, kunt u [hier](failover-failback-overview.md) meer lezen over de verschillende soorten failovers.
