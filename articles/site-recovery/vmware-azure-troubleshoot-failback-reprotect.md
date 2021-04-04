---
title: Problemen met failback in VMware VM-nood herstel oplossen met Azure Site Recovery
description: In dit artikel worden manieren beschreven voor het oplossen van problemen met failback en opnieuw beveiligen tijdens de nood herstel van een VMware-VM naar Azure met Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: ed4e52470264441a99c5ccf0a736bb00233510c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87423113"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Problemen met failback naar on-premises vanuit Azure oplossen

In dit artikel wordt beschreven hoe u problemen oplost die kunnen optreden wanneer u back-ups van virtuele Azure-machines naar uw on-premises VMware-infra structuur uitvoert, nadat u de failover naar Azure hebt uitgevoerd met behulp van [Azure site Recovery](site-recovery-overview.md).

Voor failback zijn in feite twee belang rijke stappen vereist. Voor de eerste stap moet u na een failover de Azure-Vm's opnieuw beveiligen naar on-premises zodat ze worden gerepliceerd. De tweede stap bestaat uit het uitvoeren van een failover vanuit Azure om een failback uit te voeren naar uw on-premises site.

## <a name="common-issues"></a>Algemene problemen

- Als u met een alleen-lezen gebruiker een vCenter-detectie uitvoert en virtuele machines beveiligt, slaagt de beveiliging en werkt de failover. Tijdens het opnieuw beveiligen mislukt de failover omdat de gegevens opslag niet kan worden gedetecteerd. Een symptoom is dat de gegevens opslag niet wordt weer gegeven tijdens het opnieuw beveiligen. U kunt dit probleem oplossen door de vCenter-referenties bij te werken met een geschikt account dat machtigingen heeft en de taak vervolgens opnieuw uit te voeren.
- Wanneer u een back-up van een virtuele Linux-machine maakt en deze on-premises uitvoert, kunt u zien dat het pakket met de netwerk beheerder is verwijderd van de computer. Dit wordt veroorzaakt doordat het pakket voor netwerk beheer wordt verwijderd wanneer de virtuele machine wordt hersteld in Azure.
- Wanneer een virtuele Linux-machine is geconfigureerd met een statisch IP-adres en failover naar Azure wordt uitgevoerd, wordt het IP-adres verkregen van DHCP. Wanneer u een failover naar on-premises doorvoert, blijft de virtuele machine DHCP gebruiken voor het verkrijgen van het IP-adres. Meld u hand matig aan bij de computer en stel het IP-adres vervolgens weer in op een statisch adres, indien nodig. Een virtuele Windows-machine kan het vaste IP-adres opnieuw verkrijgen.
- Als u de ESXi 5,5 Free Edition of de gratis versie van vSphere 6 Hyper Visor gebruikt, mislukt de failover, maar failback mislukt. Als u failback wilt inschakelen, moet u een upgrade uitvoeren naar de evaluatie licentie van het programma.
- Als u de configuratie server niet kunt bereiken vanaf de proces server, gebruikt u Telnet om de verbinding met de configuratie server op poort 443 te controleren. U kunt ook proberen de configuratie server te pingen vanaf de proces server. Een proces server moet ook een heartbeat hebben als deze is verbonden met de configuratie server.
- Een Windows Server 2008 R2 SP1-server die wordt beveiligd als een fysieke on-premises server kan niet worden teruggezet van Azure naar een on-premises site.
- U kunt geen failback uitvoeren in de volgende situaties:
    - U hebt computers gemigreerd naar Azure. 
    - U hebt een virtuele machine naar een andere resource groep verplaatst.
    - U hebt de Azure-VM verwijderd.
    - U hebt de beveiliging van de virtuele machine uitgeschakeld.
    - U hebt de virtuele machine hand matig in azure gemaakt. De machine moet eerst on-premises en failover naar Azure worden uitgevoerd voordat de beveiliging opnieuw wordt beschermd.
    - U kunt alleen een failover uitvoeren naar een ESXi-host. U kunt geen failback uitvoeren voor virtuele VMware-machines of fysieke servers naar Hyper-V-hosts, fysieke machines of VMware-werk stations.


## <a name="troubleshoot-reprotection-errors"></a>Fouten bij het opnieuw beveiligen oplossen

In deze sectie vindt u informatie over veelvoorkomende problemen met opnieuw beveiligen en hoe u deze kunt corrigeren.

### <a name="error-code-95226"></a>Fout code 95226

**Het opnieuw beveiligen is mislukt omdat de virtuele Azure-machine de on-premises configuratie server niet kan bereiken.**

Deze fout treedt op wanneer:

* De Azure-VM kan de on-premises configuratie server niet bereiken. De virtuele machine kan niet worden gedetecteerd en geregistreerd op de configuratie server.
* De inmage Scout-toepassings service wordt niet uitgevoerd op de virtuele Azure-machine na een failover. De service is vereist voor communicatie met de on-premises configuratie server.

Ga als volgt te werk om het probleem op te lossen:

* Controleer of het VM-netwerk van Azure toestaat dat de Azure VM communiceert met de on-premises configuratie server. U kunt een site-naar-site-VPN instellen voor uw on-premises Data Center of een Azure ExpressRoute-verbinding configureren met privé-peering op het virtuele netwerk van de Azure VM.
* Als de virtuele machine kan communiceren met de on-premises configuratie server, meldt u zich aan bij de virtuele machine. Controleer vervolgens de toepassings service inmage Scout. Als u ziet dat deze niet wordt uitgevoerd, start u de service hand matig. Controleer of het start type van de service is ingesteld op **automatisch**.

### <a name="error-code-78052"></a>Fout code 78052

**De beveiliging kan niet worden voltooid voor de virtuele machine.**

Dit probleem kan zich voordoen als er al een virtuele machine met dezelfde naam op de hoofddoel server is die u niet meer gebruikt.

Ga als volgt te werk om het probleem op te lossen:

* Selecteer een andere Master doel server op een andere host zodat de computer op een andere host wordt gemaakt, waarbij de namen niet conflicteren.
* U kunt VMotion ook gebruiken om het hoofd doel te verplaatsen naar een andere host waar de naam conflicten niet optreden. Als de bestaande VM een losse machine is, wijzigt u de naam zodat de nieuwe VM op dezelfde ESXi-host kan worden gemaakt.


### <a name="error-code-78093"></a>Fout code 78093

**De VM wordt niet uitgevoerd, reageert niet of is niet toegankelijk.**

Ga als volgt te werk om het probleem op te lossen:

Als u een failover-VM opnieuw wilt beveiligen, moet de virtuele machine van Azure worden uitgevoerd zodat de Mobility-service wordt geregistreerd bij de on-premises configuratie server en kan worden gerepliceerd door te communiceren met de proces server. Als de computer zich op een onjuist netwerk bevindt of niet wordt uitgevoerd (niet reageert of wordt afgesloten), kan de configuratie server de Mobility-service niet bereiken op de VM om de herbeveiliging te starten.

* Start de VM opnieuw op zodat deze on-premises kan communiceren.
* Start de taak opnieuw beveiligen opnieuw nadat u de virtuele machine van Azure hebt gestart.

### <a name="error-code-8061"></a>Fout code 8061

**De gegevens opslag is niet toegankelijk vanaf de ESXi-host.**

Controleer de [vereisten voor het hoofd doel en ondersteunde gegevens archieven](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) voor failback.


## <a name="troubleshoot-failback-errors"></a>Failback-fouten oplossen

In deze sectie worden veelvoorkomende fouten beschreven die kunnen optreden tijdens het failback.

### <a name="error-code-8038"></a>Fout code 8038

**Het online zetten van de on-premises virtuele machine is mislukt vanwege de fout.**

Dit probleem treedt op wanneer de on-premises VM wordt geactiveerd op een host waarvoor onvoldoende geheugen is ingericht. 

Ga als volgt te werk om het probleem op te lossen:

* Richt meer geheugen in op de ESXi-host.
* Daarnaast kunt u met VMotion de virtuele machine verplaatsen naar een andere ESXi-host die voldoende geheugen heeft om de virtuele machine op te starten.
