---
title: Versneld netwerken inschakelen voor nood herstel door Azure VM met Azure Site Recovery
description: Hierin wordt beschreven hoe u versneld netwerken met Azure Site Recovery voor nood herstel van virtuele Azure-machines inschakelt
services: site-recovery
documentationcenter: ''
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 1d2d3b3aacc00428c96cde0f8230421a98151ae2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000246"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Versneld netwerken met nood herstel voor Azure virtual machine

Versneld netwerken maken gebruik van I/O-virtualisatie met één hoofdmap (SR-IOV) naar een virtuele machine, waardoor de netwerk prestaties aanzienlijk worden verbeterd. Dit pad met hoge prestaties omzeilt de host van de DataPath, vermindert latentie, jitter en CPU-gebruik, voor gebruik met de meest veeleisende netwerk workloads op ondersteunde VM-typen. In de volgende afbeelding ziet u communicatie tussen twee Vm's met en zonder versneld netwerken:

![Vergelijking](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Met Azure Site Recovery kunt u gebruikmaken van de voor delen van versneld netwerken, voor virtuele Azure-machines waarvoor een failover wordt uitgevoerd naar een andere Azure-regio. In dit artikel wordt beschreven hoe u versneld netwerken kunt inschakelen voor virtuele Azure-machines die met Azure Site Recovery worden gerepliceerd.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende weten:
-   Azure virtual machine- [replicatie architectuur](azure-to-azure-architecture.md)
-   [Replicatie instellen](azure-to-azure-tutorial-enable-replication.md) voor virtuele Azure-machines
-   [Failover uitvoeren](azure-to-azure-tutorial-failover-failback.md) Virtuele machines van Azure

## <a name="accelerated-networking-with-windows-vms"></a>Versneld netwerken met Windows-Vm's

Azure Site Recovery ondersteunt het inschakelen van versneld netwerken alleen voor gerepliceerde virtuele machines als de virtuele bron machine versneld netwerken heeft ingeschakeld. Als uw virtuele bron machine geen versneld netwerken heeft ingeschakeld, kunt u [hier](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)meer informatie vinden over het inschakelen van versnelde netwerken voor virtuele Windows-machines.

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende distributies worden ondersteund uit het vak van de Azure-galerie:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren
Versnelde netwerken worden ondersteund in de meeste algemene doel stellingen en met Compute geoptimaliseerde exemplaar grootten met twee of meer Vcpu's.  Deze ondersteunde reeksen zijn: D/DSv2 en F/FS

Op instanties die HyperThreading ondersteunen, wordt versneld netwerken ondersteund op VM-exemplaren met vier of meer Vcpu's. Ondersteunde reeksen zijn: D/DSv3, E-ESv3, Fsv2 en MS/MMS

Zie [Windows VM-grootten](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over VM-exemplaren.

## <a name="accelerated-networking-with-linux-vms"></a>Versneld netwerken met virtuele Linux-machines

Azure Site Recovery ondersteunt het inschakelen van versneld netwerken alleen voor gerepliceerde virtuele machines als de virtuele bron machine versneld netwerken heeft ingeschakeld. Als uw virtuele bron machine geen versneld netwerken heeft ingeschakeld, kunt u [hier](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)meer informatie vinden over het inschakelen van versnelde netwerken voor virtuele Linux-machines.

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende distributies worden ondersteund uit het vak van de Azure-galerie:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian ' Stretch ' met backports-kernel**
* **Oracle Linux 7,4**

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren
Versnelde netwerken worden ondersteund in de meeste algemene doel stellingen en met Compute geoptimaliseerde exemplaar grootten met twee of meer Vcpu's.  Deze ondersteunde reeksen zijn: D/DSv2 en F/FS

Op instanties die HyperThreading ondersteunen, wordt versneld netwerken ondersteund op VM-exemplaren met vier of meer Vcpu's. Ondersteunde reeksen zijn: D/DSv3, E/ESv3, Fsv2 en MS/MMS.

Zie [Linux VM-grootten](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over VM-exemplaren.

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Versneld netwerken inschakelen voor gerepliceerde Vm's

Wanneer u replicatie voor virtuele Azure-machines [inschakelt](azure-to-azure-tutorial-enable-replication.md) , detecteert site Recovery automatisch of de netwerk interfaces van de virtuele machine versneld netwerken hebben ingeschakeld. Als versneld netwerken al is ingeschakeld, configureert Site Recovery automatisch versneld netwerken op de netwerk interfaces van de gerepliceerde virtuele machine.

De status van versneld netwerken kan worden gecontroleerd onder het gedeelte **netwerk interfaces** van de **reken-en netwerk** instellingen voor de gerepliceerde virtuele machine.

![Instelling voor versneld netwerk](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Als u versneld netwerken op de virtuele bron machine hebt ingeschakeld na het inschakelen van replicatie, kunt u versneld netwerken inschakelen voor de netwerk interfaces van de gerepliceerde virtuele machine door het volgende proces:
1. **Berekenings-en netwerk** instellingen voor de gerepliceerde virtuele machine openen
2. Klik op de naam van de netwerk interface in het gedeelte **netwerk interfaces**
3. Selecteer **ingeschakeld** in de vervolg keuzelijst voor versneld netwerken onder de **doel** kolom

![Versneld netwerken inschakelen](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Het bovenstaande proces moet ook worden gevolgd voor bestaande gerepliceerde virtuele machines, waarbij niet eerder versnelde netwerken automatisch zijn ingeschakeld door Site Recovery.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [voor delen van versneld netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Meer informatie over beperkingen en beperkingen van versneld netwerken voor [virtuele Windows-machines](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) en [virtuele Linux-machines](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Meer informatie over [herstel plannen](site-recovery-create-recovery-plans.md) voor het automatiseren van de failover van de toepassing.