---
title: Windows Server 2003-servers voorbereiden voor migratie met Azure Migrate
description: Meer informatie over het voorbereiden van Windows Server 2003-servers voor migratie met Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753741"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Windows Server 2003-machines voorbereiden voor migratie

In dit artikel wordt beschreven hoe u computers met Windows Server 2003 voorbereidt voor migratie naar Azure. 


> [!NOTE]
> De [uitgebreide ondersteuning voor Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) is gestopt op 14 juli 2015.  Het ondersteunings team van Azure blijft hulp bij het oplossen van problemen met het uitvoeren van Windows Server 2003 op Azure. Deze ondersteuning is echter beperkt tot problemen waarvoor geen probleem oplossing of patches op besturingssysteem niveau is vereist. Het migreren van uw toepassingen naar Azure-exemplaren met een nieuwere versie van Windows Server is de aanbevolen methode om ervoor te zorgen dat u de flexibiliteit en betrouw baarheid van de Azure-Cloud effectief kunt benutten. Als u echter toch uw Windows Server 2003 naar Azure migreert, kunt u de Azure Migrate: hulp programma voor server migratie gebruiken als uw Windows Server een VM is die wordt uitgevoerd op VMware of Hyper-V.


- U kunt migratie zonder agent gebruiken om [virtuele Hyper-V-machines](tutorial-migrate-hyper-v.md) en [virtuele VMware-machines](tutorial-migrate-vmware.md) naar Azure te migreren.
- Als u na de migratie verbinding wilt maken met virtuele Azure-machines, moet u de Hyper-V-integratie Services installeren op de virtuele Azure-machine. Windows Server 2003-computers hebben dit niet standaard geïnstalleerd.
- Er is geen direct download koppeling voor het installeren van Hyper-V-integratie Services, dus moet u het volgende doen:
    - Voor virtuele Hyper-V-machines die niet zijn geïnstalleerd, extraheert u installatie bestanden voor integratie Services op een computer met Windows Server 2012 R2/Windows Server 2012 met de Hyper-V-rol en kopieert u het installatie programma naar de Windows Server 2003-computer. De installatie bestanden zijn niet beschikbaar op computers met Windows Server 2016.
    - Voor virtuele VMware-machines maakt u een opstart taak waarmee integratie services worden geïnstalleerd wanneer de Azure VM na de migratie wordt gestart.


## <a name="install-on-hyper-v-vms"></a>Installeren op virtuele Hyper-V-machines

Controleer vóór de migratie of Hyper-V-integratie Services zijn geïnstalleerd en Installeer indien nodig de installatie.

1. Volg [deze instructies](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) om te controleren of de app is geïnstalleerd.
2. Als dit niet is geïnstalleerd, meldt u zich aan bij een computer met Windows Server 2012 R2/Windows Server 2012 met de Hyper-V-functie.
3. Ga naar het installatie bestand op **C:\Windows\System32\vmguest.ISO** en koppel het bestand.
2. Kopieer de installatiemap naar de Windows Server 2003-computer en installeer de integratie Services.
4. Na de installatie kunt u de standaard instellingen in integratie Services laten staan. 

## <a name="install-on-vmware-vms"></a>Installeren op virtuele VMware-machines

1. Meld u aan bij een computer met Windows Server 2012 R2/Windows Server 2012 met de Hyper-V-functie.
2. Ga naar het installatie bestand op **C:\Windows\System32\vmguest.ISO** en koppel het bestand.
3. Kopieer de installatiemap naar de virtuele VMware-machine.
4. Voer uit vanaf de opdracht regel op de virtuele machine ```gpedit.msc``` .
5. Open **computer configuratie**  >  **Windows instellingen**  >  **scripts (opstarten/afsluiten)**.
6. Typ in **opstart**  >    >  **script naam** toevoegen het setup.exe adres.
7. Na de migratie naar Azure wordt het script uitgevoerd op het moment dat de Azure VM voor het eerst wordt gestart.
8. Start de Azure-VM hand matig opnieuw op. Er wordt een pop-up in de diagnostische gegevens over opstarten weer gegeven om aan te geven dat de computer opnieuw moet worden opgestart.
9. Nadat het script is uitgevoerd en Hyper-V Integration Services is geïnstalleerd op de virtuele Azure-machine, kunt u het script verwijderen uit het opstarten.
10. Na de installatie kunt u de standaard instellingen in integratie Services laten staan. 

## <a name="next-steps"></a>Volgende stappen

- Bekijk de migratie vereisten voor [VMware](migrate-support-matrix-vmware-migration.md) -en [Hyper-V-](migrate-support-matrix-hyper-v-migration.md) vm's.
- Migreer [VMware](server-migrate-overview.md) [-en Hyper-V-](tutorial-migrate-hyper-v.md) vm's.
