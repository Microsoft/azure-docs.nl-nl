---
title: Netwerk adapters beheren voor on-premises nood herstel met Azure Site Recovery
description: Hierin wordt beschreven hoe u netwerk interfaces beheert voor on-premises herstel na nood gevallen naar Azure met Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: harshacs
ms.openlocfilehash: 4dad7f76edf34782131c7c844978763cda53acc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90068112"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>VM-netwerkinterfaces beheren voor herstel na noodgevallen van on-premises naar Azure

Aan een virtuele machine (VM) in azure moet minstens één netwerk interface zijn gekoppeld. Er kunnen zoveel netwerk interfaces aan zijn gekoppeld als de VM-grootte ondersteunt.

Standaard wordt de eerste netwerk interface die is gekoppeld aan een virtuele machine van Azure gedefinieerd als de primaire netwerk interface. Alle andere netwerk interfaces in de virtuele machine zijn secundaire netwerk interfaces. Standaard wordt al het uitgaande verkeer van de virtuele machine verzonden met het IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerk interface.

In een on-premises omgeving kunnen virtuele machines of servers meerdere netwerk interfaces hebben voor verschillende netwerken binnen de omgeving. Verschillende netwerken worden meestal gebruikt voor het uitvoeren van specifieke bewerkingen, zoals upgrades, onderhoud en Internet toegang. Wanneer u migreert of een failover uitvoert naar Azure vanuit een on-premises omgeving, moet u er rekening mee gehouden dat netwerk interfaces in dezelfde virtuele machine allemaal moeten zijn verbonden met hetzelfde virtuele netwerk.

Azure Site Recovery maakt standaard net zoveel netwerk interfaces op een virtuele Azure-machine als die zijn verbonden met de on-premises server. U kunt voor komen dat u redundante netwerk interfaces maakt tijdens de migratie of failover door de instellingen voor de netwerk interface te bewerken onder de instellingen voor de gerepliceerde virtuele machine.

## <a name="select-the-target-network"></a>Het doelnet netwerk selecteren

Voor VMware en fysieke machines en voor Hyper-V (zonder System Center Virtual Machine Manager) virtuele machines kunt u het virtuele doelnet werk voor afzonderlijke virtuele machines opgeven. Voor virtuele Hyper-V-machines die worden beheerd met Virtual Machine Manager, gebruikt u [netwerk toewijzing](./hyper-v-vmm-network-mapping.md) om VM-netwerken te koppelen aan een virtuele bron machine Manager-server en doel-Azure-netwerken.

1. Selecteer bij **gerepliceerde items** in een Recovery Services kluis een gerepliceerd item voor toegang tot de instellingen voor het gerepliceerde item.

2. Selecteer het tabblad **Compute en netwerk** om toegang te krijgen tot de netwerk instellingen voor het gerepliceerde item.

3. Kies onder **netwerk eigenschappen** een virtueel netwerk in de lijst met beschik bare netwerk interfaces.

    ![Netwerkinstellingen](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Het wijzigen van het doel netwerk is van invloed op alle netwerk interfaces voor die specifieke virtuele machine.

Voor Virtual Machine Manager-Clouds wijzigt u netwerk toewijzing van invloed op alle virtuele machines en hun netwerk interfaces.

## <a name="select-the-target-interface-type"></a>Het type doel interface selecteren

In het gedeelte **netwerk interfaces** van het deel venster **Compute en netwerk** kunt u instellingen voor netwerk interfaces weer geven en bewerken. U kunt ook het interface type van het doelnet doel opgeven.

- Er is een **primaire** netwerk interface vereist voor failover.
- Alle andere geselecteerde netwerk interfaces, indien aanwezig, zijn **secundaire** netwerk interfaces.
- Selecteer **niet gebruiken** om het maken van een netwerk interface bij een failover uit te sluiten.

Wanneer u replicatie inschakelt, selecteert Site Recovery standaard alle gedetecteerde netwerk interfaces op de on-premises server. Er wordt één as **Primary** en alle andere als **secundair** gemarkeerd. Alle volgende interfaces die zijn toegevoegd op de on-premises server, worden **niet standaard gebruikt** . Wanneer u meer netwerk interfaces toevoegt, moet u ervoor zorgen dat de juiste doel grootte van de virtuele machine van Azure is geselecteerd voor alle vereiste netwerk interfaces.

## <a name="modify-network-interface-settings"></a>Instellingen voor netwerk interface wijzigen

U kunt het subnet en het IP-adres voor de netwerk interfaces van een gerepliceerd item wijzigen. Als er geen IP-adres is opgegeven, wijst Site Recovery het volgende beschik bare IP-adres van het subnet toe aan de netwerk interface bij failover.

1. Selecteer een beschik bare netwerk interface om de netwerk interface-instellingen te openen.

2. Kies het gewenste subnet in de lijst met beschik bare subnetten.

3. Voer het gewenste IP-adres in (indien nodig).

    ![Netwerk interface-instellingen](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Selecteer **OK** om de bewerking te volt ooien en terug te gaan naar het deel venster **Compute en netwerk** .

5. Herhaal stap 1-4 voor andere netwerk interfaces.

6. Selecteer **Opslaan** om alle wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen
  Meer [informatie](../virtual-network/virtual-network-network-interface-vm.md) over netwerk interfaces voor virtuele machines van Azure.
