---
title: In Azure Site Recovery failover van virtuele Hyper-V-machines naar Azure instellen
description: Meer informatie over het uitvoeren van een failover van virtuele Hyper-V-machines naar Azure in Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132464"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Een failover van Virtuele Hyper-V-machines naar Azure uitvoeren

In deze zelfstudie wordt beschreven hoe u met [Azure Site Recovery](site-recovery-overview.md) een failover uitvoert van virtuele Hyper-V-machines naar Azure. Nadat u een failover hebt uitgevoerd, valt u terug op uw on-premises site wanneer deze beschikbaar is. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleren of de eigenschappen van de virtuele Hyper-V-machine in overeenstemming zijn met de Azure-vereisten.
> * Failover uitvoeren van specifieke VM's naar Azure.


Dit is de vijfde zelfstudie in een reeks. In deze zelfstudie wordt ervan uitgegaan dat u de taken in de vorige zelfstudies al hebt voltooid.    

1. [Azure voorbereiden](tutorial-prepare-azure.md)
2. [On-premises Hyper-V voorbereiden](./hyper-v-prepare-on-premises-tutorial.md)
3. Noodherstel configureren voor [virtuele Hyper-V-machines](./hyper-v-azure-tutorial.md) of voor [virtuele Hyper-V-machines die worden beheerd in System Center VMM-clouds](./hyper-v-vmm-azure-tutorial.md)
4. [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)

[Meer informatie](failover-failback-overview.md#types-of-failover) over verschillende typen failover. Als u een failover wilt uitvoeren voor meerdere VM's in een herstelplan, raadpleegt u [dit artikel](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Voorbereiden op een failover 
Zorg ervoor dat er zich geen momentopnamen bevinden op de virtuele machine en dat de on-premises virtuele machine is uitgeschakeld tijdens failback. Dit helpt ervoor te zorgen dat gegevens tijdens de replicatie consistent blijven. Schakel de on-premises virtuele machine niet in tijdens failback. 

Failover en failback bestaat uit drie fasen:

1. **Failover naar Azure**: failover van virtuele Hyper-V-machines van de on-premises site naar Azure.
2. **Failback naar on-premises**: failover van virtuele Azure-machines naar uw on-premises site als de on-premises site beschikbaar is. Gegevens worden gesynchroniseerd van Azure naar on-premises en als dat is afgerond, worden de virtuele machines on-premises online gezet.  
3. **Replicatie omkeren op on-premises VM's**: nadat failback naar on-premises is voltooid, keert u de replicatie van de on-premises virtuele machines om, zodat deze weer worden gerepliceerd naar Azure.

## <a name="verify-vm-properties"></a>VM-eigenschappen verifiëren

Controleer voorafgaand aan failover de eigenschappen van de virtuele machine om er zeker van te zijn dat de machine voldoet aan de [Azure-vereisten](hyper-v-azure-support-matrix.md#replicated-vms).

Klik in **Beveiligde items** op **Gerepliceerde items** > VM.

1. In het deelvenster **Gerepliceerd item** bevindt zich een overzicht van VM-informatie, status, en de laatste beschikbare herstelpunten. Klik op **Eigenschappen** om meer details te bekijken.

1. In **Berekening en netwerk** kunt u de Azure-naam, de resourcegroep, de doelgrootte, [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md) en instellingen van de beheerde schijf wijzigen

1. U kunt de netwerkinstellingen bekijken en wijzigen, inclusief het netwerk-/subnet waarin de Azure VM zich na failover bevindt en het IP-adres dat eraan wordt toegewezen.

1. In **Schijven** ziet u informatie over het besturingssysteem en de gegevensschijven van de VM.

## <a name="fail-over-to-azure"></a>Failover naar Azure

1. Klik in **Instellingen** > **Gerepliceerde items** op de VM > **Failover**.
2. Selecteer bij **Failover** **Meest recent** als het herstelpunt. 
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery probeert virtuele bronmachines af te sluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
4. Na het controleren van de failover, klikt u op **Doorvoeren**. Hiermee worden alle beschikbare herstelpunten verwijderd.

> [!WARNING]
> **Annuleer nooit een failover die in uitvoering is**: als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.

## <a name="connect-to-failed-over-vm"></a>Verbinding maken met VM waarvoor failover is uitgevoerd

1. Als u na een failover verbinding wilt maken met Azure-VM's met behulp van Remote Desktop Protocol (RDP) en Secure Shell (SSH), [controleert u of aan de vereisten is voldaan](failover-failback-overview.md#connect-to-azure-after-failover).
2. Na de failover gaat u naar de VM en valideert u door [verbinding te maken](../virtual-machines/windows/connect-logon.md) met deze machine.
3. Gebruik **Herstelpunt wijzigen** als u na een failover een ander herstelpunt wilt gebruiken. Nadat u de failover in de volgende stap hebt doorgevoerd, is deze optie niet meer beschikbaar.
4. Na validatie selecteert u **Doorvoeren** om het herstelpunt van de VM na een failover te voltooien.
5. Na het doorvoeren worden alle andere herstelpunten verwijderd. Met deze stap wordt de failover voltooid.

>[!TIP]
> Volg de handleiding voor het [oplossen van problemen](site-recovery-failover-to-azure-troubleshoot.md) als u na een failover connectiviteitsproblemen ondervindt.


## <a name="next-steps"></a>Volgende stappen

Na een failover moet u de Azure-VM's opnieuw beveiligen zodat deze van Azure naar on-premises worden gerepliceerd. Nadat de VM's opnieuw zijn beveiligd en gerepliceerd naar de on-premises site, kunt u een failback uitvoeren vanuit Azure wanneer u klaar bent.
