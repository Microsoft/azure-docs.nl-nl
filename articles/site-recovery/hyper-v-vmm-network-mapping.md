---
title: Informatie over Hyper-V (met VMM) netwerk toewijzing met Site Recovery
description: Hierin wordt beschreven hoe u netwerk toewijzing instelt voor herstel na nood gevallen van virtuele Hyper-V-machines (beheerd in VMM-Clouds) naar Azure, met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74082562"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Netwerk toewijzing voorbereiden voor herstel na nood geval voor Hyper-V-VM naar Azure


Dit artikel helpt u bij het begrijpen en voorbereiden van netwerk toewijzing wanneer u virtuele Hyper-V-machines in System Center Virtual Machine Manager-Clouds (VMM) repliceert naar Azure of naar een secundaire site met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Netwerk toewijzing voorbereiden voor replicatie naar Azure

Wanneer u repliceert naar Azure, worden netwerk toewijzingen toegewezen tussen VM-netwerken op een Bron-VMM-server en doel-Azure virtuele netwerken. Bij toewijzing gebeurt het volgende:
-  **Netwerk verbinding**: zorgt ervoor dat gerepliceerde Azure-vm's zijn verbonden met het toegewezen netwerk. Alle computers die een failover op hetzelfde netwerk uitvoeren, kunnen met elkaar worden verbonden, zelfs als ze zijn opgetreden in verschillende herstel plannen.
- **Netwerk gateway**: als er een netwerk gateway is ingesteld op het doel-Azure-netwerk, kunnen vm's verbinding maken met andere on-premises virtuele machines.

Netwerk toewijzing werkt als volgt:

- U wijst een Bron-VMM VM-netwerk toe aan een virtueel Azure-netwerk.
- Na failover van virtuele machines in het bron netwerk wordt er verbinding gemaakt met het toegewezen virtuele netwerk.
- Nieuwe Vm's die worden toegevoegd aan het bron-VM-netwerk, worden verbonden met het toegewezen Azure-netwerk wanneer replicatie plaatsvindt.
- Als het doelnetwerk meerdere subnetten bevat en een van deze subnetten dezelfde naam heeft als het subnet waarin de virtuele bronmachine zich bevindt, wordt de gerepliceerde virtuele machine na een failover verbonden met dat doelsubnet.
- Als er geen doelsubnet met een overeenkomende naam bestaat, wordt de virtuele machine verbonden met het eerste subnet in het netwerk.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Netwerk toewijzing voorbereiden voor replicatie naar een secundaire site

Wanneer u repliceert naar een secundaire site, worden netwerk toewijzingen toegewezen tussen VM-netwerken op een Bron-VMM-server en VM-netwerken op een doel-VMM-server. Bij toewijzing gebeurt het volgende:

- **Netwerk verbinding**: Hiermee worden virtuele machines verbonden met de juiste netwerken na een failover. De replica-VM wordt verbonden met het doelnet doel netwerk dat is toegewezen aan het bron netwerk.
- **Optimale VM-plaatsing**: de virtuele replica-Vm's op Hyper-V-hostservers worden optimaal geplaatst. Replica-Vm's worden geplaatst op hosts die toegang hebben tot de toegewezen VM-netwerken.
- **Geen netwerk toewijzing**: als u geen netwerk toewijzing configureert, worden replica-vm's na een failover niet verbonden met VM-netwerken.

Netwerk toewijzing werkt als volgt:

- Netwerk toewijzing kan worden geconfigureerd tussen VM-netwerken op twee VMM-servers of op één VMM-server als twee sites door dezelfde server worden beheerd.
- Wanneer toewijzing correct is geconfigureerd en replicatie is ingeschakeld, wordt een virtuele machine op de primaire locatie verbonden met een netwerk en wordt de replica op de doel locatie verbonden met het toegewezen netwerk.
- Wanneer u tijdens het netwerk toewijzing in Site Recovery een doel-VM-netwerk selecteert, worden de VMM-bron Clouds weer gegeven die gebruikmaken van het bron-VM-netwerk, samen met de beschik bare VM-netwerken die worden gebruikt voor de beveiliging van de doel Clouds.
- Als het doelnet werk meerdere subnetten heeft en een van deze subnetten dezelfde naam heeft als het subnet waarop de virtuele bron machine zich bevindt, wordt de replica-VM na een failover verbonden met het betreffende doel-subnet. Als er geen doel-subnet met een overeenkomende naam is, wordt de virtuele machine verbonden met het eerste subnet in het netwerk.

## <a name="example"></a>Voorbeeld

Hier volgt een voor beeld om dit mechanisme te illustreren. We nemen een organisatie met twee locaties in New York en Chicago.

**Locatie** | **VMM-server** | **VM-netwerken** | **Toegewezen aan**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Toegewezen aan VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Niet toegewezen
Chicago | VMM-Chicago| VMNetwork1-Chicago | Toegewezen aan VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Niet toegewezen

In dit voorbeeld:

- Wanneer een replica-VM wordt gemaakt voor een virtuele machine die is verbonden met VMNetwork1-NewYork, wordt deze verbonden met VMNetwork1-Chicago.
- Wanneer een replica-VM wordt gemaakt voor VMNetwork2-NewYork of VMNetwork2-Chicago, wordt deze niet verbonden met een netwerk.

Hier ziet u hoe VMM-Clouds worden ingesteld in de voor beeld-organisatie en welke logische netwerken aan de Clouds zijn gekoppeld.

### <a name="cloud-protection-settings"></a>Instellingen voor Cloud beveiliging

**Beveiligde Cloud** | **Cloud beveiligen** | **Logisch netwerk (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Logische en VM-netwerk instellingen

**Locatie** | **Logisch netwerk** | **Gekoppeld VM-netwerk**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Instellingen van het doelnet netwerk

Op basis van deze instellingen, wanneer u het doel-VM-netwerk selecteert, worden in de volgende tabel de opties weer gegeven die beschikbaar zullen zijn.

**Selecteren** | **Beveiligde Cloud** | **Cloud beveiligen** | **Doelnet netwerk beschikbaar**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Beschikbaar
 | GoldCloud1 | GoldCloud2 | Beschikbaar
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Niet beschikbaar
 | GoldCloud1 | GoldCloud2 | Beschikbaar


Als het doelnet werk meerdere subnetten heeft en een van deze subnetten dezelfde naam heeft als het subnet waarop de virtuele bron machine zich bevindt, wordt de replica virtuele machine na een failover verbonden met het betreffende doel-subnet. Als er geen doelsubnet met een overeenkomende naam bestaat, wordt de virtuele machine verbonden met het eerste subnet in het netwerk.


### <a name="failback-behavior"></a>Failback-gedrag

Als u wilt zien wat er gebeurt in het geval van een failback (omgekeerde replicatie), gaan we ervan uit dat VMNetwork1-NewYork is toegewezen aan VMNetwork1-Chicago, met de volgende instellingen.


**VM** | **Verbonden met het VM-netwerk**
---|---
VM1 | VMNetwork1-Network
VM2 (replica van VM1) | VMNetwork1-Chicago

Met deze instellingen gaan we kijken wat er gebeurt in een aantal mogelijke scenario's.

**Scenario** | **Daarvan**
---|---
Geen wijziging in de netwerk eigenschappen van VM-2 na een failover. | VM-1 blijft verbonden met het bron netwerk.
De netwerk eigenschappen van VM-2 worden na de failover gewijzigd en de verbinding is verbroken. | De verbinding met VM-1 is verbroken.
De netwerk eigenschappen van VM-2 worden na een failover gewijzigd en zijn verbonden met VMNetwork2-Chicago. | Als VMNetwork2-Chicago niet is toegewezen, wordt de verbinding met VM-1 verbroken.
De netwerk toewijzing van VMNetwork1-Chicago is gewijzigd. | VM-1 wordt nu verbonden met het netwerk toegewezen aan VMNetwork1-Chicago.



## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](hyper-v-vmm-networking.md) IP-adres Sering na een failover naar een secundaire VMM-site.
- [Meer informatie over](concepts-on-premises-to-azure-networking.md) IP-adres Sering na failover naar Azure.
