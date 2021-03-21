---
title: VIRTUELE Hyper-V-schijven uitsluiten van herstel na nood gevallen naar Azure met Azure Site Recovery
description: VIRTUELE Hyper-V-schijven uitsluiten van replicatie naar Azure met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86131251"
---
# <a name="exclude-disks-from-replication"></a>Schijven uitsluiten van replicatie

In dit artikel wordt beschreven hoe u schijven uitsluit wanneer u virtuele Hyper-V-machines naar Azure repliceert. Mogelijk wilt u schijven uitsluiten van replicatie om een aantal redenen:

- Zorg ervoor dat niet-belang rijke gegevens die op de uitgesloten schijf worden overgevoerd, niet worden gerepliceerd.
- Optimaliseer de verbruikte replicatie bandbreedte of de bronnen aan de doel zijde door de schijven uit te sluiten die u niet hoeft te repliceren.
- Sla opslag-en netwerk bronnen op door geen gegevens te repliceren die u niet nodig hebt.

Voordat u schijven uitsluit van replicatie:

- Meer [informatie](exclude-disks-replication.md) over het uitsluiten van schijven.
- Bekijk [typische uitsluitings scenario's](exclude-disks-replication.md#typical-scenarios) en [voor beelden](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) die laten zien hoe de replicatie, failover en failback wordt beïnvloed door een schijf.

## <a name="before-you-start"></a>Voordat u begint

Let op het volgende voordat u begint:

- **Replicatie**: alle schijven op een computer worden standaard gerepliceerd.
- **Schijf type**:
    - U kunt basis schijven uitsluiten van replicatie.
    - Besturingssysteemschijven kunt u niet uitsluiten.
    - We raden u aan geen dynamische schijven uit te sluiten. Site Recovery kunt niet identificeren welke VHD Basic of Dynamic is in de gast-VM.  Als u niet alle afhankelijke dynamische volume schijven uitsluit, wordt de beveiligde dynamische schijf een defecte schijf op een failover-VM. de gegevens op de schijf zijn dan niet toegankelijk.
- **Schijven toevoegen/verwijderen/uitsluiten**: wanneer u replicatie inschakelt, kunt u geen schijven toevoegen/verwijderen of uitsluiten voor replicatie. Als u een schijf wilt toevoegen/verwijderen of uitsluiten, moet u de beveiliging voor de virtuele machine uitschakelen en vervolgens weer inschakelen.
- **Failover**: na een failover, moet u de schijven hand matig maken als er een fout is opgetreden in apps die niet meer nodig zijn om te werken. U kunt Azure Automation ook integreren in een herstel plan om de schijf te maken tijdens de failover van de machine.
- **Failback**: wanneer u na een failover een failback naar uw on-premises site hebt uitgevoerd, worden schijven die u hand matig hebt gemaakt in azure niet meer teruggezet. Als u bijvoorbeeld een failover hebt uitgevoerd voor drie schijven en twee schijven rechtstreeks op een virtuele Azure-machine maakt, worden er slechts drie schijven met een failover-fout weer gegeven. U kunt geen schijven toevoegen die hand matig zijn gemaakt in de failback of in een omgekeerde replicatie van Vm's.

## <a name="exclude-disks"></a>Schijven uitsluiten

1. Als u schijven wilt uitsluiten wanneer u [replicatie inschakelt](./hyper-v-azure-tutorial.md) voor een Hyper-V-VM, moet u na het selecteren van de virtuele machines die u wilt repliceren op de pagina eigenschappen van **replicatie inschakelen**  >    >  **configureren** de kolom **te repliceren schijven** controleren. Standaard zijn alle schijven geselecteerd voor replicatie.
2. Als u een specifieke schijf niet wilt repliceren, wist u de selectie van de schijven die u wilt uitsluiten als u de schijven wilt **repliceren** . 

    ![Schijven uitsluiten van replicatie](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Volgende stappen
Wanneer uw implementatie actief is, kunt u [hier](failover-failback-overview.md) meer lezen over de verschillende soorten failovers.
