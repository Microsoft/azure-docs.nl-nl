---
title: Upgrade uitvoeren van Windows Server/System Center VMM 2012 R2 naar Windows Server 2016-Azure Site Recovery
description: Meer informatie over het upgraden van Windows Server 2012 R2-hosts & SCVMM 2012 R2 die zijn geconfigureerd met Azure Site Recovery naar Windows Server 2016 & SCVMM 2016.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: sharrai
ms.openlocfilehash: b3df487d690befadd249142c449163c2393f6df6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640332"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Upgrade uitvoeren van Windows Server Server/System Center 2012 R2 VMM naar Windows Server/VMM 2016 

In dit artikel wordt beschreven hoe u Windows Server 2012 R2-hosts bijwerkt & SCVMM 2012 R2 die zijn geconfigureerd met Azure Site Recovery, naar Windows Server 2016 & SCVMM 2016

Site Recovery draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR). De service zorgt ervoor dat de werk belasting van uw virtuele machines beschikbaar blijven als verwacht en onverwachte storingen optreden.

> [!IMPORTANT]
> Wanneer u Windows Server 2012 R2-hosts bijwerkt die al zijn geconfigureerd voor replicatie met Azure Site Recovery, moet u de stappen volgen die in dit document worden beschreven. Elk alternatief pad dat voor de upgrade is gekozen, kan leiden tot niet-ondersteunde statussen en kan leiden tot een storing in de replicatie of de mogelijkheid om een failover uit te voeren.


In dit artikel leert u hoe u de volgende configuraties in uw omgeving kunt bijwerken:

> [!div class="checklist"]
> * **Windows Server 2012 R2-hosts die niet worden beheerd door SCVMM** 
> * **Windows Server 2012 R2-hosts die worden beheerd door een zelfstandige SCVMM 2012 R2-server** 
> * **Windows Server 2012 R2-hosts die worden beheerd door Maxi maal beschik bare SCVMM 2012 R2 server**


## <a name="prerequisites--factors-to-consider"></a>Vereisten & factoren om rekening mee te houden

Let op het volgende voordat u een upgrade uitvoert:-

- Als u Windows Server 2012 R2-hosts hebt die niet worden beheerd door SCVMM en een zelfstandige omgevings instelling is, wordt de replicatie onderbroken als u de upgrade probeert uit te voeren.
- Als u '*niet mijn sleutels in Active Directory onder gedistribueerd sleutel beheer*' had geselecteerd tijdens de installatie van SCVMM 2012 R2 in de eerste plaats, worden de upgrades niet voltooid.

- Als u System Center 2012 R2 VMM gebruikt, 

    - Controleer de database gegevens op VMM:   ->    ->  **algemene**  ->  **database verbinding** voor de instellingen van de VMM-console
    - Controleer de service accounts die worden gebruikt voor de System Center Virtual Machine Manager Agent-service
    - Zorg ervoor dat u een back-up van de VMM-Data Base hebt.
    - Noteer de naam van de data base van de betrokken SCVMM-servers. Dit kan worden gedaan door te navigeren naar de **VMM-console**-  ->  **instellingen**  ->  **algemene**  ->  **database verbinding**
    - Noteer de VMM-ID van de 2012R2 primaire en herstel VMM-servers. VMM-ID kan worden gevonden in het REGI ster HKLM: \ SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup.
    - Zorg ervoor dat de nieuwe SCVMMs die u aan het cluster toevoegt, dezelfde namen hebben als voorheen. 

- Als u repliceren tussen twee van uw sites die aan beide zijden worden beheerd door SCVMMs, moet u eerst de herstel kant upgraden voordat u de primaire zijde bijwerkt.
  > [!WARNING]
  > Selecteer tijdens het bijwerken van de SCVMM 2012 R2 onder gedistribueerd sleutel beheer om **versleutelings sleutels op te slaan in Active Directory**. Kies de instellingen voor het service account en gedistribueerd sleutel beheer zorgvuldig. Op basis van uw selectie zijn versleutelde gegevens, zoals wacht woorden in sjablonen, mogelijk niet beschikbaar na de upgrade en kunnen de replicatie met Azure Site Recovery mogelijk beïnvloeden.

> [!IMPORTANT]
> Raadpleeg de gedetailleerde documentatie over de SCVMM van [vereisten](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Windows Server 2012 R2-hosts die niet worden beheerd door SCVMM 
De lijst met stappen die hieronder worden beschreven, is van toepassing op de gebruikers configuratie van [Hyper-V-hosts naar Azure](./hyper-v-azure-architecture.md) die wordt uitgevoerd door deze [zelf studie](./hyper-v-prepare-on-premises-tutorial.md) te volgen

> [!WARNING]
> Zoals vermeld in de vereisten, zijn deze stappen alleen van toepassing op een scenario met een geclusterde omgeving en niet in een zelfstandige Hyper-V-host-configuratie.

1. Volg de stappen om de [Rolling cluster upgrade](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) uit te voeren. om het upgrade proces voor Rolling clusters uit te voeren.
2. Voor elke nieuwe Windows Server 2016-host die in het cluster is geïntroduceerd, verwijdert u de verwijzing van een Windows Server 2012 R2-host uit Azure Site Recovery door de volgende stappen te volgen: [hier]. Dit moet de host zijn die u hebt gekozen om & weghalen uit het cluster te verwijderen.
3. Zodra de opdracht *Update-VMVersion* is uitgevoerd voor alle virtuele machines, zijn de upgrades voltooid. 
4. Volg de stappen die [hier](./hyper-v-azure-tutorial.md#set-up-the-source-environment) worden beschreven om de nieuwe Windows Server 2016-host te registreren bij Azure site Recovery. De Hyper-V-site is al actief en u hoeft alleen de nieuwe host in het cluster te registreren. 
5. Ga naar het Azure Portal en controleer de status van de gerepliceerde status in de Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Een upgrade uitvoeren van Windows Server 2012 R2-hosts die worden beheerd door zelfstandige SCVMM 2012 R2-server
Voordat u uw Windows Server 2012 R2-hosts bijwerkt, moet u de SCVMM 2012 R2 upgraden naar SCVMM 2016. Volg de onderstaande stappen:-

**Zelfstandige versie van SCVMM 2012 R2 upgraden naar SCVMM 2016**

1.  Verwijder de ASR-provider door te navigeren naar configuratie scherm-> Program Ma's-> Program Ma's en onderdelen->Microsoft Azure Site Recovery en klik op verwijderen
2. [De SCVMM-data base behouden en het besturings systeem upgraden](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#back-up-and-upgrade-the-operating-system)
3. Selecteer in **Program ma's toevoegen** de optie **VMM**  >  **verwijderen**. b. Selecteer **onderdelen verwijderen** en selecteer vervolgens V **mm-beheer server en VMM-console**. c. Selecteer in **database opties** de optie **Data Base behouden**. d. Controleer de samen vatting en klik op **verwijderen**.

4. [VMM 2016 installeren](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#install-vmm-2016)
5. Start SCVMM en controleer de status van elke host onder het tabblad **fabrics** . Klik op **vernieuwen** om de meest recente status te krijgen. U ziet de status ' attentie vereist '. 
17.    Installeer de nieuwste [Microsoft Azure site Recovery provider](https://aka.ms/downloaddra) op de SCVMM.
16.    Installeer de meest recente [Mars-agent (Microsoft Azure Recovery Service)](https://aka.ms/latestmarsagent) op elke host van het cluster. Vernieuwen om ervoor te zorgen dat SCVMM kan zoeken naar de hosts.

**Windows Server 2012 R2-hosts upgraden naar Windows Server 2016**

1. Volg de stappen die [hier](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) worden beschreven om het upgrade proces voor Rolling clusters uit te voeren. 
2. Nadat u de nieuwe host aan het cluster hebt toegevoegd, moet u de host vernieuwen vanuit de SCVMM-console om de VMM-agent op deze bijgewerkte host te installeren.
3. Voer *Update-VMVersion* uit om de VM-versies van de virtuele machines bij te werken. 
4. Ga naar de Azure Portal en controleer de status van de gerepliceerde virtuele machines in de Recovery Services kluis. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Upgraden van Windows Server 2012 R2-hosts worden beheerd door Maxi maal beschik bare SCVMM 2012 R2 server
Voordat u uw Windows Server 2012 R2-hosts bijwerkt, moet u de SCVMM 2012 R2 upgraden naar SCVMM 2016. De volgende upgrade methoden worden ondersteund tijdens het bijwerken van de SCVMM 2012 R2-servers die zijn geconfigureerd met Azure Site Recovery-gemengde modus zonder extra VMM-servers & gemengde modus met aanvullende VMM-servers.

**Upgrade SCVMM 2012 R2 naar SCVMM 2016**

1.  Verwijder de ASR-provider door te navigeren naar configuratie scherm-> Program Ma's-> Program Ma's en onderdelen->Microsoft Azure Site Recovery en klik op verwijderen
2. Volg de stappen die [hier](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#upgrade-a-standalone-vmm-server) worden beschreven op basis van de modus van de upgrade die u wilt uitvoeren.
3. Start de SCVMM-console en controleer de status van elke host onder het tabblad **fabrics** . Klik op **vernieuwen** om de meest recente status te krijgen. U ziet de status ' attentie vereist '.
4. Installeer de nieuwste [Microsoft Azure site Recovery provider](https://aka.ms/downloaddra) op de SCVMM.
5. Werk de meest recente [Mars-agent (Microsoft Azure Recovery Service)](https://aka.ms/latestmarsagent) bij op elke host van het cluster. Vernieuwen om ervoor te zorgen dat SC VMM de hosts kan doorzoeken.


**Windows Server 2012 R2-hosts upgraden naar Windows Server 2016**

1. Volg de stappen die [hier](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) worden beschreven om het upgrade proces voor Rolling clusters uit te voeren.
2. Nadat u de nieuwe host aan het cluster hebt toegevoegd, moet u de host vernieuwen vanuit de SCVMM-console om de VMM-agent op deze bijgewerkte host te installeren.
3. Voer *Update-VMVersion* uit om de VM-versies van de virtuele machines bij te werken. 
4. Ga naar de Azure Portal en controleer de status van de gerepliceerde virtuele machines in de Recovery Services kluis. 

## <a name="next-steps"></a>Volgende stappen
Zodra de upgrade van de hosts is uitgevoerd, kunt u een testfailover uitvoeren om de status van uw replicatie en herstel na nood gevallen [te testen.](tutorial-dr-drill-azure.md)
