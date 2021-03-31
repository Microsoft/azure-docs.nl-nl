---
title: Failback van virtuele VMware-machines/fysieke servers van Azure met Azure Site Recovery
description: Meer informatie over het uitvoeren van een failback naar de on-premises site na een failover naar Azure, tijdens nood herstel van virtuele VMware-machines en fysieke servers naar Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004191"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Failback van virtuele VMware-machines naar een on-premises site

In dit artikel wordt beschreven hoe u back-ups van virtuele Azure-machines naar een on-premises site kunt herstellen, na het uitvoeren van een [failover](site-recovery-failover.md) van on-premises Vm's naar Azure met [Azure site Recovery](site-recovery-overview.md). Na failback naar on-premises schakelt u replicatie in zodat de on-premises Vm's beginnen met repliceren naar Azure.

## <a name="before-you-start"></a>Voordat u begint

1. Meer informatie over [VMware-failback](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Zorg ervoor dat u de stappen voor het [voorbereiden op een failback](vmware-azure-prepare-failback.md) hebt gecontroleerd en voltooid, en dat alle vereiste onderdelen zijn geïmplementeerd. Onderdelen bevatten een proces server in azure, een on-premises hoofddoel server en een VPN-site-naar-site-verbinding (of ExpressRoute privé-peering) voor failback.
3. Zorg ervoor dat u de [vereisten](vmware-azure-reprotect.md#before-you-begin) voor opnieuw beveiligen en failback hebt voltooid en dat u het opnieuw beveiligen van virtuele Azure-machines hebt [ingeschakeld](vmware-azure-reprotect.md#enable-reprotection) , zodat deze vanuit Azure worden gerepliceerd naar de on-premises site. VM’s moeten een gerepliceerde status hebben voordat een failback kan worden uitgevoerd voor deze VM’s.




## <a name="run-a-failover-to-fail-back"></a>Een failover uitvoeren naar failback

1. Zorg ervoor dat de virtuele machines van Azure opnieuw worden beveiligd en gerepliceerd naar de on-premises site.
    - Een VM moet minstens één herstelpunt hebben om een failback te kunnen uitvoeren.
    - Als u een failback voor een herstelplan uitvoert, moeten alle machines in het abonnement minstens één herstelpunt hebben.
2. Selecteer de VM in de kluis > **Gerepliceerde items**. Klik met de rechtermuisknop op de VM > **Niet-geplande failover**.
3. Controleer in **Failover bevestigen** de failoverrichting (van Azure).
4. Selecteer het herstelpunt dat u wilt gebruiken voor de failover.
    - We raden u aan het **Meest recente** herstelpunt te gebruiken. Het app-consistente punt bevindt zich achter het laatste tijdstip en zorgt voor enig gegevensverlies.
    - **Meest recent** is een crash-consistent herstelpunt.
    - Met **Meest recent** wordt een VM hersteld naar het laatst beschikbare tijdstip. Als u een replicatiegroep hebt voor consistentie voor meerdere VM’s binnen een herstelplan, wordt voor elke VM in de groep een failover uitgevoerd naar het bijbehorende onafhankelijke meest recente tijdstip.
    - Als u een app-consistent herstelpunt gebruikt, wordt voor elke VM een failback uitgevoerd naar het laatste beschikbare punt. Als een herstelplan een replicatiegroep heeft, herstelt elke groep naar het bijbehorende algemeen beschikbare herstelpunt.
5. Failover begint. Met Site Recovery worden de Azure-VM's afgesloten.
6. Wanneer de failover is voltooid, controleert u of alles werkt zoals verwacht. Controleer of de Azure-VM’s zijn afgesloten. 
7. Nu alles is gecontroleerd, klikt u met de rechtermuisknop op de VM > **Doorvoeren** om het failoverproces te voltooien. Met doorvoeren wordt de Azure-VM verwijderd waarvoor de failover is uitgevoerd. 

> [!NOTE]
> Voor Windows-VM’s worden met Site Recovery de VMware-hulpprogramma’s uitgeschakeld tijdens de failover. Tijdens de failback van de Windows-VM zijn de VMware-hulpprogramma's opnieuw ingeschakeld. 




## <a name="reprotect-from-on-premises-to-azure"></a>Opnieuw beveiligen van on-premises naar Azure

Nadat de failback is doorgevoerd, worden de Azure-VM’s verwijderd. De virtuele machine bevindt zich weer op de on-premises site, maar is niet beveiligd. U kunt als volgt opnieuw beginnen met repliceren van VM’s naar Azure:

1. Selecteer in de kluis > **Gerepliceerde items** de VM’s waarvoor een failback is uitgevoerd, en selecteer **Opnieuw beveiligen**.
2. Geef de processerver op die wordt gebruikt om gegevens terug te verzenden naar Azure.
3. Selecteer **OK** om te beginnen met de taak voor opnieuw beveiligen.

> [!NOTE]
> Nadat een on-premises VM is gestart, duurt het Maxi maal 15 minuten voordat de agent zich opnieuw registreerde op de configuratie server. Tijdens deze periode mislukt het opnieuw beveiligen, en wordt een foutbericht geretourneerd waarin staat dat de agent niet is geïnstalleerd. Als dit gebeurt, wacht u enkele minuten, en beveiligt u opnieuw.

## <a name="next-steps"></a>Volgende stappen

Nadat de taak opnieuw beveiligen is voltooid, wordt de on-premises VM gerepliceerd naar Azure. U kunt [nog een failover uitvoeren](site-recovery-failover.md) naar Azure, indien nodig.

