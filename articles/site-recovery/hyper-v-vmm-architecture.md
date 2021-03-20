---
title: Architectuur-herstel na nood geval voor Hyper-V naar een secundaire site met Azure Site Recovery
description: Dit artikel bevat een overzicht van de architectuur voor herstel na nood gevallen van on-premises virtuele Hyper-V-machines naar een secundaire System Center VMM-site met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 703a6afdc12c8a9863ff0f480ec7a577ec31ef77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87495995"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architectuur-Hyper-V-replicatie naar een secundaire site

In dit artikel worden de onderdelen en processen beschreven die zijn betrokken bij het repliceren van on-premises Hyper-V-VM's (virtuele machines) in System Center VMM-clouds (Virtual Machine Manager) naar een secundaire VMM-site met behulp van de [Azure Site Recovery](site-recovery-overview.md)-service in Azure Portal.
een

## <a name="architectural-components"></a>Architectuuronderdelen

De volgende tabel en afbeelding bieden een weer gave op hoog niveau van de onderdelen die worden gebruikt voor Hyper-V-replicatie naar een secundaire site.

**Onderdeel** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Azure-abonnement | U maakt een Recovery Services-kluis in het Azure-abonnement om de replicatie tussen VMM-locaties te organiseren en te beheren.
**VMM-server** | U hebt een primaire en secundaire VMM-locatie nodig. | Aanbevolen is een VMM-beheerserver op de primaire site en een op de secundaire site.
**Hyper-V-Server** |  U hebt een of meer Hyper-V-hostservers nodig die zich bevinden in de primaire en secundaire VMM-clouds. | Gegevens worden tussen de primaire en secundaire Hyper-V-hostservers via het LAN of VPN gerepliceerd met behulp van Kerberos of verificatie via certificaat.  
**Virtuele Hyper-V-machines** | Op Hyper-V-hostserver. | De bronhostserver moet ten minste één VM hebben die u wilt repliceren.

**On-premises naar on-premises architectuur**

![Diagram van on-premises naar on-premises beveiliging.](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replicatieproces

1. Wanneer de initiële replicatie wordt geactiveerd, wordt een [Hyper-V VM](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) -moment opname gemaakt.
2. Virtuele harde schijven op de virtuele machine worden een voor een gerepliceerd naar de secundaire locatie.
3. Als er wijzigingen in de schijf optreden terwijl de eerste replicatie wordt uitgevoerd, worden de wijzigingen door de Hyper-V replica Replication tracker bijgehouden als Hyper-V-replicatie Logboeken (. HRL). Deze logboek bestanden bevinden zich in dezelfde map als de schijven. Elke schijf heeft een bijbehorend. HRL-bestand dat wordt verzonden naar de secundaire locatie. De momentopname- en logboekbestanden nemen schijfbronnen in beslag terwijl de eerste replicatie wordt uitgevoerd.
4. Wanneer de initiële replicatie is voltooid, wordt de moment opname van de virtuele machine verwijderd en begint de replicatie van verschillen.
5. Verschillen in het logboek worden gesynchroniseerd en samengevoegd op de bovenliggende schijf.


## <a name="failover-and-failback-process"></a>Failover- en failbackproces

- U kunt een failover van één machine uitvoeren of herstelplannen maken om de failover van meerdere virtuele machines te coördineren.
- U kunt een geplande of niet-geplande failover uitvoeren tussen on-premises sites. Als u een geplande failover uitvoert, worden de virtuele bronmachines afgesloten om gegevensverlies te voorkomen.
    - Als u een niet-geplande failover naar een secundaire site uitvoert, worden de failover-machines op de secundaire locatie niet beveiligd.
    - Als u een geplande failover hebt uitgevoerd, zijn de machines op de secundaire locatie na de failover beveiligd.
- Nadat de eerste failover is uitgevoerd, kunt u deze door voeren om toegang te krijgen tot de werk belasting vanaf de replica-VM.
- Wanneer de primaire locatie weer beschikbaar is, kunt u een failback uitvoeren.
    - U initieert omgekeerde replicatie om te beginnen met het repliceren van de secundaire site naar de primaire. Omgekeerde replicatie geeft de virtuele machines een beveiligde status, maar het secundaire datacenter is nog steeds de actieve locatie.
    - Als u de primaire site weer de actieve locatie wilt maken, start u een geplande failover van de secundaire site naar de primaire site, gevolgd door nog een omgekeerde replicatie.



## <a name="next-steps"></a>Volgende stappen


Volg [deze zelf studie](hyper-v-vmm-disaster-recovery.md) om Hyper-V-replicatie tussen VMM-Clouds in te scha kelen.
