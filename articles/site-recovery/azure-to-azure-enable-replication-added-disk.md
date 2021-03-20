---
title: Replicatie inschakelen voor een toegevoegde Azure VM-schijf in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u replicatie inschakelt voor een schijf die is toegevoegd aan een virtuele Azure-machine die is ingeschakeld voor herstel na nood gevallen met Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75973795"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Replicatie inschakelen voor een schijf die is toegevoegd aan een Azure VM


In dit artikel wordt beschreven hoe u replicatie inschakelt voor gegevens schijven die zijn toegevoegd aan een Azure-VM die al is ingeschakeld voor herstel na nood gevallen naar een andere Azure-regio, met behulp van [Azure site Recovery](site-recovery-overview.md).

Het inschakelen van replicatie voor een schijf die u toevoegt aan een virtuele machine wordt ondersteund voor virtuele Azure-machines met beheerde schijven.

Wanneer u een nieuwe schijf toevoegt aan een virtuele machine van Azure die wordt gerepliceerd naar een andere Azure-regio, gebeurt het volgende:

-   Bij de replicatie status voor de VM wordt een waarschuwing weer gegeven en een opmerking in de portal meldt dat er een of meer schijven beschikbaar zijn voor beveiliging.
-   Als u beveiliging inschakelt voor de toegevoegde schijven, verdwijnt de waarschuwing na de initiële replicatie van de schijf.
-   Als u ervoor kiest geen replicatie voor de schijf in te scha kelen, kunt u de waarschuwing negeren.

![Nieuwe schijf toegevoegd](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u nood herstel al hebt ingesteld voor de virtuele machine waaraan u de schijf wilt toevoegen. Als dat niet het geval is, volgt u de [zelf studie over Azure nood herstel](azure-to-azure-tutorial-enable-replication.md).

## <a name="enable-replication-for-an-added-disk"></a>Replicatie inschakelen voor een toegevoegde schijf

Ga als volgt te werk om replicatie in te scha kelen voor een toegevoegde schijf:

1. Klik in de kluis > **gerepliceerde items** op de virtuele machine waaraan u de schijf hebt toegevoegd.
2. Klik op **schijven** en selecteer vervolgens de gegevens schijf waarvoor u replicatie wilt inschakelen (deze schijven hebben een niet- **beveiligde** status).
3.  Klik in **schijf Details** op **replicatie inschakelen**.

    ![Replicatie inschakelen voor toegevoegde schijf](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Nadat de taak replicatie inschakelen is uitgevoerd en de initiële replicatie is voltooid, wordt de replicatie status waarschuwing voor het probleem van de schijf verwijderd.



## <a name="next-steps"></a>Volgende stappen

[Meer informatie](site-recovery-test-failover-to-azure.md) over het uitvoeren van een testfailover.
