---
title: VMware-Vm's opnieuw beveiligen naar een on-premises site met Azure Site Recovery
description: Meer informatie over het opnieuw beveiligen van VMware-Vm's na een failover naar Azure met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 6a11e3d0cb41383b44b76975ecbd1c2ae2825015
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89441490"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Opnieuw beveiligen van Azure naar on-premises

Na een [failover](site-recovery-failover.md) van on-premises virtuele VMware-machines of fysieke servers naar Azure, is de eerste stap bij het uitvoeren van een failback naar uw on-premises site het opnieuw beveiligen van de Azure-vm's die zijn gemaakt tijdens de failover. In dit artikel wordt beschreven hoe u dit doet. 

## <a name="before-you-begin"></a>Voordat u begint

1. Volg de stappen in [dit artikel](vmware-azure-prepare-failback.md) om de beveiliging en failback voor te bereiden, inclusief het instellen van een proces server in Azure en een on-premises hoofddoel server, en het configureren van een site-naar-site-VPN, of ExpressRoute privé-peering, voor failback.
2. Zorg ervoor dat de on-premises configuratie server actief is en is verbonden met Azure. Tijdens een failover naar Azure is de on-premises site mogelijk niet toegankelijk en is de configuratie server mogelijk niet beschikbaar of afgesloten. Tijdens de failback moet de VM aanwezig zijn in de database van de configuratieserver. Anders mislukt de failback.
3. Verwijder alle moment opnamen op de on-premises hoofddoel server. Opnieuw beveiligen werkt niet als er schaduwkopieën zijn.  De schaduwkopieën op de VM worden automatisch samengevoegd tijdens de taak voor opnieuw beveiligen.
4. Als u VM's die voor consistentie tussen meerdere VM's zijn gecombineerd tot een replicatiegroep opnieuw beveiligt, moet u ervoor zorgen dat ze allemaal hetzelfde besturingssysteem hebben (Windows of Linux), en dat de hoofddoelserver die u implementeert hetzelfde type besturingssysteem heeft. Alle VM's in een replicatiegroep moeten dezelfde hoofddoelserver gebruiken.
5. Open [de vereiste poorten](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) voor failback.
6. Zorg ervoor dat de vCenter Server is verbonden vóór de failback. Anders mislukt het ontkoppelen en weer toevoegen van schijven aan de virtuele machine.
7. Als de VM's waarnaar u een failback wilt uitvoeren, worden beheerd door vCenter Server, zorg er dan voor dat u de juiste rechten hebt. Als u met een alleen-lezen gebruiker een vCenter-detectie uitvoert en virtuele machines beveiligt, slaagt de beveiliging en werkt de failover. Tijdens het opnieuw beveiligen mislukt de failover echter, omdat de gegevensarchieven niet kunnen worden gedetecteerd en niet worden vermeld tijdens het opnieuw beveiligen. U kunt dit probleem oplossen door de vCenter-referenties bij te werken met [een geschikt account/de juiste machtigingen](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) en de taak vervolgens opnieuw uit te voeren. 
8. Als u een sjabloon hebt gebruikt om uw virtuele machines te maken, moet u ervoor zorgen dat elke virtuele machine een eigen UUID voor de schijven heeft. Als de UUID van de on-premises VM afstamt met de UUID van de hoofddoel server omdat beide zijn gemaakt op basis van dezelfde sjabloon, mislukt de beveiliging. Implementeer op basis van een andere sjabloon.
9. Als u geen failback uitvoert naar een alternatieve vCenter Server, moet u ervoor zorgen dat de nieuwe vCenter Server en de hoofddoelserver worden gedetecteerd. Als ze niet worden gedetecteerd, zijn de gegevensarchieven normaal gesproken niet toegankelijk of zijn ze niet zichtbaar in **Opnieuw beveiligen**.
10. Controleer de volgende scenario's waarin u geen failback kunt uitvoeren:
    - Als u gebruikmaakt van de ESXi 5.5 Free Edition of de gratis versie van vSphere 6 Hyper Visor. Voer een upgrade uit naar een andere versie.
    - Als u een fysieke server met Windows Server 2008 R2 SP1 hebt.
    - VMware-VM's kunnen geen failback uitvoeren naar Hyper-V.
    - VM's die zijn gemigreerd.
    - Een VM die is verplaatst naar een andere resourcegroep.
    - Een replica van een Azure-VM die is verwijderd.
    - Een replica van een virtuele machine van Azure die niet is beveiligd (repliceren naar de on-premises site).
10. [Bekijk de typen failback](concepts-types-of-failback.md) die u kunt gebruiken: herstel naar de oorspronkelijke locatie en herstel naar een alternatieve locatie.


## <a name="enable-reprotection"></a>Opnieuw beveiligen inschakelen

Schakel replicatie in. U kunt specifieke VM's of een herstelplan opnieuw beveiligen:

- Als u een herstelplan opnieuw beveiligt, moet u de waarden voor elke beveiligde machine opgeven.
- Als Vm's deel uitmaken van een replicatiegroep voor consistentie tussen meerdere VM's, kunnen ze alleen opnieuw worden beveiligd met behulp van een herstelplan. VM's in een replicatiegroep moeten dezelfde hoofddoelserver gebruiken

>[!NOTE]
>De hoeveelheid gegevens die tijdens het opnieuw beveiligen van Azure naar de vroegere bron wordt verzonden, kan alles zijn tussen 0 bytes en de totale schijfgrootte voor alle beveiligde machines, en kan niet worden berekend.

### <a name="before-you-start"></a>Voordat u begint

- Nadat een virtuele machine na een failover in Azure is opgestart, duurt het enige tijd voordat de agent opnieuw is geregistreerd bij de configuratieserver (tot 15 minuten). Gedurende deze tijd kunt u niet opnieuw beveiligen en wordt er een foutbericht weergegeven dat de agent niet is geïnstalleerd. Als dat gebeurt, wacht u enkele minuten en beveiligt u vervolgens opnieuw.
- Als u een back-up wilt maken van de virtuele machine van Azure naar een bestaande on-premises VM, koppelt u de on-premises VM-gegevens opslag items met lees-en schrijf toegang op de ESXi-host van de hoofddoel server.
- Als u een failback wilt uitvoeren naar een alternatieve locatie, bijvoorbeeld als de on-premises VM niet bestaat, selecteert u het Bewaar station en de gegevens opslag die zijn geconfigureerd voor de hoofddoel server. Wanneer u een failback naar de on-premises site maakt, gebruiken de virtuele VMware-machines in het failback-beveiligings plan dezelfde gegevens opslag als de hoofddoel server. Er wordt vervolgens een nieuwe VM gemaakt in vCenter.

Schakel opnieuw beveiligen als volgt in:

1. Selecteer **Kluis** > **Gerepliceerde items**. Klik met de rechtermuisknop op de virtuele machine waarvoor een failover is uitgevoerd en selecteer **Opnieuw beveiligen**. U kunt ook met de opdrachtknoppen de machine selecteren en vervolgens **Opnieuw beveiligen** selecteren.
2. Controleer of de beveiligingsrichting **Azure naar on-premises** is geselecteerd.
3. Selecteer bij **Hoofddoelserver** en **Processerver** de on-premises hoofddoelserver en de processerver.  
4. Selecteer voor **gegevens opslag** de gegevens opslag waarnaar u de schijven on-premises wilt herstellen. Deze optie wordt gebruikt wanneer de on-premises virtuele machine wordt verwijderd en u moet nieuwe schijven maken. Deze optie wordt genegeerd als de schijven al bestaan. U moet toch een waarde opgeven.
5. Selecteer het bewaarstation.
6. Het failback-beleid wordt automatisch geselecteerd.
7. Selecteer **OK** om te beginnen met opnieuw beveiligen.

    ![Het dialoogvenster Opnieuw beveiligen](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Een taak begint met het repliceren van de virtuele machine van Azure naar de on-premises site. U kunt de voortgang volgen op het tabblad **Taken**.
    - Wanneer het opnieuw beveiligen is geslaagd, komt de VM in een beveiligde staat.
    - De on-premises VM wordt uitgeschakeld tijdens het opnieuw beveiligen. Dit helpt ervoor te zorgen dat gegevens tijdens de replicatie consistent blijven.
    - Schakel de on-premises VM niet in nadat de beveiliging is voltooid.
   

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt, raadpleegt u het [artikel over het oplossen van problemen](vmware-azure-troubleshoot-failback-reprotect.md).
- Nadat de Azure-VM's zijn beveiligd, kunt u [een failback uitvoeren](vmware-azure-failback.md). De virtuele machine van Azure wordt afgesloten en de on-premises VM wordt opgestart. Houd rekening met enige downtime voor de toepassing, en kies een geschikte tijd voor de failback.


