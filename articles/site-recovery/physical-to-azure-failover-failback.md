---
title: Failover en failback instellen voor fysieke servers met Site Recovery
description: Meer informatie over het uitvoeren van een failover van fysieke servers naar Azure en het uitvoeren van een failback naar de on-premises site voor herstel na nood gevallen met Azure Site Recovery
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.openlocfilehash: 68427d264b6d2eea1a38e3b8bc5273409221c126
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579906"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Failover en failback van fysieke servers die zijn gerepliceerd naar Azure

In deze zelf studie wordt beschreven hoe u een failover van on-premises fysieke servers die repliceren naar Azure met [Azure site Recovery](site-recovery-overview.md), kunt uitvoeren. Nadat u een failover hebt uitgevoerd, keert u terug van Azure naar uw on-premises site wanneer deze beschikbaar is.

## <a name="before-you-start"></a>Voordat u begint

- [Meer informatie](failover-failback-overview.md) over het failoverproces in nood herstel.
- Als u een failover wilt uitvoeren voor meerdere machines, [leert](recovery-plan-overview.md) u hoe u computers samen kunt verzamelen in een herstel plan.
- Voordat u een volledige failover uitvoert, moet u een [nood herstel analyse](site-recovery-test-failover-to-azure.md) uitvoeren om te controleren of alles werkt zoals verwacht.
- Volg [deze instructies](site-recovery-failover.md#prepare-to-connect-after-failover) om voor te bereiden om verbinding te maken met virtuele Azure-machines na een failover.



## <a name="run-a-failover"></a>Een failover uitvoeren

### <a name="verify-server-properties"></a>Server eigenschappen controleren

Controleer de server eigenschappen en controleer of deze voldoet aan de [Azure-vereisten](vmware-physical-azure-support-matrix.md#replicated-machines) voor Azure-vm's.

1. Klik in **beveiligde items** op **gerepliceerde items** en selecteer de computer.
2. In het deel venster **gerepliceerd item** bevindt zich een samen vatting van de computer gegevens, de integriteits status en de meest recente beschik bare herstel punten. Klik op **Eigenschappen** om meer details te bekijken.
3. In **Compute en Network** kunt u de Azure-naam, de resource groep, de doel grootte, [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md)en de instellingen voor beheerde schijven wijzigen
4. U kunt de netwerkinstellingen bekijken en wijzigen, inclusief het netwerk-/subnet waarin de Azure VM zich na failover bevindt en het IP-adres dat eraan wordt toegewezen.
5. In **schijven** kunt u informatie over het besturings systeem en de gegevens schijven van de machine bekijken.

### <a name="fail-over-to-azure"></a>Failover naar Azure

1. Klik in **Instellingen** > **Gerepliceerde items** op de machine > **Failover**.
2. Selecteer in **Failover** een **Herstelpunt** waarnaar u de failover wilt uitvoeren. U kunt een van de volgende opties gebruiken:
   - **Laatste**: Met deze optie worden eerst alle gegevens verwerkt die naar Site Recovery zijn verzonden. Dit biedt het laagste RPO (Recovery Point Objective), omdat de na de failover gemaakte Azure-VM alle gegevens heeft die naar Site Recovery is gerepliceerd toen de failover werd geactiveerd.
   - **Laatst verwerkte**: met deze optie wordt er een failover uitgevoerd van de machine naar het laatste herstel punt dat is verwerkt door site Recovery. Deze optie heeft een lage RTO (Recovery Time Objective), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
   - **Nieuwste app-consistent**: met deze optie kan de computer worden overgeschakeld naar het nieuwste toepassings consistente herstel punt dat is verwerkt door site Recovery.
   - **Aangepast**: geef een herstelpunt op.

3. Selecteer **Afsluiten machine voordat u de failover start** als u wilt dat site Recovery probeert de bron machine af te sluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
4. Als u de verbinding met de Azure VM hebt voorbereid, maakt u na de failover verbinding om deze te valideren.
5. Na het verifiëren kunt u de failover **Doorvoeren**. Hiermee verwijdert u alle beschikbare herstelpunten.

> [!WARNING]
> Annuleer nooit een failover die in uitvoering is. Voordat de failover wordt gestart, wordt de machine replicatie gestopt. Als u de failover annuleert, wordt deze gestopt, maar de computer wordt niet opnieuw gerepliceerd.
> Voor fysieke servers kan een extra failover-verwerking rond acht tot tien minuten duren.

## <a name="automate-actions-during-failover"></a>Acties automatiseren tijdens failover

Mogelijk wilt u acties automatiseren tijdens failover. Hiervoor kunt u scripts of Azure Automation-runbooks gebruiken in herstel plannen.

- [Meer informatie](site-recovery-create-recovery-plans.md) over het maken en aanpassen van herstel plannen, inclusief het toevoegen van scripts.
- [Meer informatie over](site-recovery-runbook-automation.md) over het toevoegen van Azure Automation runbooks aan herstel plannen.

## <a name="configure-settings-after-failover"></a>Instellingen configureren na failover

Na een failover moet u de [Azure-instellingen configureren](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) om verbinding te maken met de gerepliceerde Azure-vm's. Daarnaast kunt u [interne en open bare](site-recovery-failover.md#set-up-ip-addressing) IP-adres Sering instellen.

## <a name="prepare-for-reprotection-and-failback"></a>Voorbereiden op opnieuw beveiligen en failback

Na een failover naar Azure, kunt u Azure-Vm's opnieuw beveiligen door ze te repliceren naar de on-premises site. Nadat ze zijn gerepliceerd, kunt u ze weer on-premises uitvoeren door een failover van Azure naar uw on-premises site uit te voeren.

1. Fysieke servers die worden gerepliceerd naar Azure met Site Recovery kunnen alleen failback uitvoeren als virtuele VMware-machines. U hebt een VMware-infra structuur nodig om een failback uit te kunnen laten. Volg de stappen in [dit artikel](vmware-azure-prepare-failback.md) om de beveiliging en failback voor te bereiden, inclusief het instellen van een proces server in Azure en een on-premises hoofddoel server, en het configureren van een site-naar-site-VPN, of ExpressRoute privé-peering, voor failback.
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
    - VM's die zijn gemigreerd.
    - Een VM die is verplaatst naar een andere resourcegroep.
    - Een replica van een Azure-VM die is verwijderd.
    - Een replica van een virtuele machine van Azure die niet is beveiligd (repliceren naar de on-premises site).
10. [Bekijk de typen failback](concepts-types-of-failback.md) die u kunt gebruiken: herstel naar de oorspronkelijke locatie en herstel naar een alternatieve locatie.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Azure-Vm's opnieuw beveiligen naar een alternatieve locatie

In deze procedure wordt ervan uitgegaan dat de on-premises VM niet beschikbaar is.

1. Klik in de kluis > **instellingen**  >  **gerepliceerde items** met de rechter muisknop op de computer waarvoor een failover is uitgevoerd > **opnieuw te beveiligen**.
2. Controleer in **Opnieuw beveiligen** of **Azure naar on-premises** is geselecteerd.
3. Geef de on-premises hoofddoelserver en de processerver op.
4. Selecteer in **Gegevensopslag** de gegevensopslag van het hoofddoel waarnaar u de on-premises schijven wilt herstellen.
       - Gebruik deze optie als de on-premises VM is verwijderd of niet bestaat en u nieuwe schijven wilt maken.
       - Deze instelling wordt genegeerd als de schijven al bestaan, maar u moet een waarde opgeven.
5. Selecteer het bewaarstation van het hoofddoel. Het failback-beleid wordt automatisch geselecteerd.
6. Klik op **OK** om te beginnen met opnieuw beveiligen. Een taak begint met het repliceren van de virtuele machine van Azure naar de on-premises site. U kunt de voortgang volgen op het tabblad **Taken**.

> [!NOTE]
> Als u de Azure-VM wilt herstellen naar een bestaande on-premises VM, koppelt u de gegevensopslag van de virtuele machine met lees-/schrijf-toegang aan de ESXi-host van de hoofddoelserver.


## <a name="fail-back-from-azure"></a>Failback vanaf Azure

Voer de failover als volgt uit:

1. Klik op de pagina **Gerepliceerde items** met de rechtermuisknop op de computer > **Niet-geplande failover**.
2. Controleer in **Failover bevestigen** of de failoverrichting van Azure is.
3. Selecteer het herstel punt dat u wilt gebruiken voor de failover.
    - We raden u aan het **Meest recente** herstelpunt te gebruiken. Het app-consistente punt bevindt zich achter het laatste tijdstip en zorgt voor enig gegevensverlies.
    - **Meest recent** is een crash-consistent herstelpunt.
    - Wanneer failover wordt uitgevoerd, schakelt Site Recovery de Azure VM's uit en start de on-premises VM op. Er zal wat downtime zijn, dus kies een geschikte tijd.
4. Klik met de rechtermuisknop op de computer en klik op **Doorvoeren**. Dit activeert een taak waardoor de Azure VM's worden verwijderd.
5. Controleer of de Azure VM's zijn afgesloten zoals verwacht.


## <a name="reprotect-on-premises-machines-to-azure"></a>On-premises computers opnieuw beveiligen naar Azure

De gegevens moeten nu weer terug zijn op uw on-premises site, maar ze worden niet gerepliceerd naar Azure. U kunt als volgt weer beginnen met repliceren naar Azure:

1. Selecteer in de kluis > **Instellingen** >**Gerepliceerde items** de failback-VM's waarvoor een failback is uitgevoerd en klik op **Opnieuw beveiligen**.
2. Selecteer de processerver die wordt gebruikt om de gerepliceerde gegevens naar Azure te verzenden en klik op **OK**.


## <a name="next-steps"></a>Volgende stappen

Nadat de taak opnieuw beveiligen is voltooid, wordt de on-premises VM gerepliceerd naar Azure. U kunt [nog een failover uitvoeren](site-recovery-failover.md) naar Azure, indien nodig.
