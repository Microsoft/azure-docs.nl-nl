---
title: Failback van Azure VMware Solution-VM’s van Azure met Azure Site Recovery
description: Krijg meer informatie over de failback naar de Azure VMware Solution-privécloud, na failover naar Azure, tijdens herstel na een noodgeval.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: fb14e647d3444f2f0d0cb86901f93582a18848f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91814194"
---
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>Failback van VM’s naar de Azure VMware Solution-privécloud

In dit artikel wordt beschreven hoe u een failback van Azure-VM’s naar een Azure VMware Solution-privécloud uitvoert, na de [failover](avs-tutorial-failover.md) van Azure VMware Solution-VM’s met [Azure Site Recovery](site-recovery-overview.md). Na de failback schakelt u replicatie in, zodat de replicatie van Azure VMware Solution-VM’s naar Azure wordt gestart.

## <a name="before-you-start"></a>Voordat u begint

1. Meer informatie over [VMware-failback](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Zorg ervoor dat u de stappen voor het [voorbereiden op een failback](vmware-azure-prepare-failback.md) hebt gecontroleerd en voltooid, en dat alle vereiste onderdelen zijn geïmplementeerd. Onderdelen omvatten een processerver in Azure, een hoofddoelserver, en een site-naar-site-verbinding naar een VPN (of ExpressRoute-privépeering) voor failback.
3. Zorg ervoor dat u de [vereisten](avs-tutorial-reprotect.md#before-you-begin) voor opnieuw beveiligen en failback hebt voltooid, en dat u [opnieuw beveiligen](avs-tutorial-reprotect.md#enable-reprotection) van Azure-VM’s hebt ingeschakeld, zodat ze repliceren van Azure naar de Azure VMware Solution-privécloud. VM’s moeten een gerepliceerde status hebben voordat een failback kan worden uitgevoerd voor deze VM’s.




## <a name="run-a-failover-to-fail-back"></a>Een failover uitvoeren naar failback

1. Zorg ervoor dat de Azure-VM's opnieuw worden beveiligd, en dat ze worden gerepliceerd naar de Azure VMware Solution-privécloud.
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




## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>Opnieuw beveiligen van Azure VMware Solution naar Azure

Nadat de failback is doorgevoerd, worden de Azure-VM’s verwijderd. De VM is terug in de Azure VMware Solution-privécloud, maar is niet beveiligd. U kunt als volgt opnieuw beginnen met repliceren van VM’s naar Azure:

1. Selecteer in de kluis > **Gerepliceerde items** de VM’s waarvoor een failback is uitgevoerd, en selecteer **Opnieuw beveiligen**.
2. Geef de processerver op die wordt gebruikt om gegevens terug te verzenden naar Azure.
3. Selecteer **OK** om te beginnen met de taak voor opnieuw beveiligen.

> [!NOTE]
> Nadat een Azure VMware Solution-VM is gestart, duurt het 15 minuten voor de agent om terug te registreren bij de configuratieserver. Tijdens deze periode mislukt het opnieuw beveiligen, en wordt een foutbericht geretourneerd waarin staat dat de agent niet is geïnstalleerd. Als dit gebeurt, wacht u enkele minuten, en beveiligt u opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als de taak voor opnieuw beveiligen is voltooid, repliceert de Azure VMware Solution-VM naar Azure. U kunt [nog een failover uitvoeren](avs-tutorial-failover.md) naar Azure, indien nodig.

