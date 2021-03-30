---
title: Failover/failback instellen op een secundaire Hyper-V-site met Azure Site Recovery
description: Meer informatie over het uitvoeren van een failover van virtuele Hyper-V-machines naar uw secundaire on-premises site en failback naar de primaire site, tijdens nood herstel met Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74082588"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Failover en failback uitvoeren van Hyper-V-Vm's die zijn gerepliceerd naar uw secundaire on-premises site

De [Azure site Recovery](site-recovery-overview.md) -service beheert en organiseert de replicatie, failover en failback van on-premises machines en virtuele Azure-machines (vm's).

In dit artikel wordt beschreven hoe u een failover van een Hyper-V-VM in een System Center Virtual Machine Manager (VMM)-Cloud, naar een secundaire VMM-site. Nadat u een failover hebt uitgevoerd, valt u terug op uw on-premises site wanneer deze beschikbaar is. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Failover van een Hyper-V-VM vanuit een primaire VMM-Cloud naar een secundaire VMM-Cloud
> * Opnieuw beveiligen van de secundaire site naar de primaire en failback
> * U kunt eventueel ook beginnen met repliceren van primair naar secundair

## <a name="failover-and-failback"></a>Failover en failback

Failover en failback hebben drie fasen:

1. **Failover naar secundaire site**: er worden geen computers meer van de primaire site naar de secundaire.
2. **Failback van de secundaire site**: repliceer vm's van secundair naar primair en voer een geplande failover uit om een failback uit te voeren.
3. Na de geplande failover begint optioneel met het repliceren van de primaire site naar de secundaire locatie.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u een [nood herstel analyse](hyper-v-vmm-test-failover.md) hebt uitgevoerd om te controleren of alles werkt zoals verwacht.
- Zorg ervoor dat de primaire en secundaire VMM-servers zijn verbonden met Site Recovery om failback te volt ooien.



## <a name="run-a-failover-from-primary-to-secondary"></a>Een failover uitvoeren van de primaire naar de secundaire

U kunt een regel matige of geplande failover uitvoeren voor virtuele Hyper-V-machines.

- Gebruik een reguliere failover voor onverwachte storingen. Wanneer u deze failover uitvoert, maakt Site Recovery een virtuele machine in de secundaire site en voert deze uit. Er kunnen gegevens verloren gaan, afhankelijk van de in behandeling zijnde gegevens die niet zijn gesynchroniseerd.
- Een geplande failover kan worden gebruikt voor onderhoud of tijdens de verwachte uitval. Deze optie zorgt ervoor dat er geen gegevens verloren gaan. Wanneer een geplande failover wordt geactiveerd, worden de bron-Vm's afgesloten. Niet-gesynchroniseerde gegevens worden gesynchroniseerd en de failover wordt geactiveerd. 
- 
  In deze procedure wordt beschreven hoe u een regel matige failover uitvoert.


1. Klik in **instellingen**  >  **gerepliceerde items** op de virtuele machine > **failover**.
1. Selecteer **Sluit de computer af voordat de failover wordt gestart** als u wilt dat Site Recovery probeert bron-VM's af te sluiten voordat de failover wordt geactiveerd. Site Recovery probeert ook on-premises gegevens te synchroniseren die nog niet zijn verzonden naar de secundaire site voordat de failover wordt geactiveerd. De failover wordt voortgezet, zelfs als het afsluiten mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
2. Nu moet u de virtuele machine in de secundaire VMM-Cloud kunnen zien.
3. Nadat u de virtuele machine hebt gecontroleerd, voert u de failover **door** . Hiermee verwijdert u alle beschikbare herstelpunten.

> [!WARNING]
> **Annuleer een failover die in voortgang is niet**: voordat de failover wordt gestart, wordt de VM-replicatie gestopt. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.  


## <a name="reverse-replicate-and-failover"></a>Omgekeerde replicatie en failover

Begin met het repliceren van de secundaire site naar de primaire site en failback naar de primaire locatie. Nadat Vm's weer actief zijn op de primaire site, kunt u ze repliceren naar de secundaire site.  

 
1. Klik op de VM > Klik op **omgekeerd repliceren**.
2. Zodra de taak is voltooid, klikt u op de virtuele machine >in **failover**, controleert u de failover-richting (vanuit de secundaire VMM-Cloud) en selecteert u de bron-en doel locatie. 
4. Start de failover. U kunt de voortgang van de failover volgen op het tabblad **Taken**.
5. Controleer in de primaire VMM-Cloud of de virtuele machine beschikbaar is.
6. Als u opnieuw wilt beginnen met het repliceren van de primaire virtuele machine naar de secundaire site, klikt u op **omgekeerd repliceren**.

## <a name="next-steps"></a>Volgende stappen
[Bekijk de stap voor het](hyper-v-vmm-disaster-recovery.md) repliceren van virtuele Hyper-V-machines naar een secundaire site.
