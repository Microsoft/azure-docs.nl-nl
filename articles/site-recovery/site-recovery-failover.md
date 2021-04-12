---
title: Een failover uitvoeren tijdens nood herstel met Azure Site Recovery
description: Failover van Vm's/fysieke servers naar Azure met Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 6737f64773f91ede1631d42cd7f28c7d961c0454
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368618"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Een failover uitvoeren van on-premises naar Azure

In dit artikel wordt beschreven hoe u een failover van on-premises machines naar Azure in [Azure site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Voordat u begint

- [Meer informatie](failover-failback-overview.md) over het failoverproces in nood herstel.
- Als u een failover wilt uitvoeren voor meerdere machines, [leert](recovery-plan-overview.md) u hoe u computers samen kunt verzamelen in een herstel plan.
- Voordat u een volledige failover uitvoert, moet u een [nood herstel analyse](site-recovery-test-failover-to-azure.md) uitvoeren om te controleren of alles werkt zoals verwacht.

## <a name="prepare-to-connect-after-failover"></a>Voorbereiden om verbinding te maken na een failover

Om ervoor te zorgen dat u verbinding kunt maken met de virtuele Azure-machines die na een failover worden gemaakt, volgt u een aantal dingen die u on-premises moet doen voordat u een failover uitvoert.


### <a name="prepare-on-premises-to-connect-after-failover"></a>On-premises voorbereiden om verbinding te maken na een failover

Als u na een failover verbinding wilt maken met virtuele Azure-machines met behulp van RDP/SSH, moet u on-premises een aantal dingen doen voordat een failover wordt uitgevoerd.

**Na een failover** | **Locatie** | **Acties**
--- | --- | ---
**Virtuele Azure-machine met Windows** | On-premises machine vóór een failover | Voor toegang tot de Azure-VM via internet schakelt u RDP in en zorgt u ervoor dat de TCP-en UDP-regels voor **openbaar** worden toegevoegd en dat RDP is toegestaan voor alle profielen in **Windows Firewall**  >  **toegestane apps**.<br/><br/> Als u toegang wilt krijgen tot de Azure-VM via een site-naar-site-verbinding, schakelt u RDP in op de machine en zorgt u ervoor dat RDP is toegestaan in de **Windows Firewall**  ->  **toegestane apps en functies** voor **domein-en particuliere** netwerken.<br/><br/> <br/><br/> Verwijder alle statische permanente routes en WinHTTP-proxy. Zorg ervoor dat het SAN-beleid van het besturings systeem is ingesteld op **OnlineAll**. [Meer informatie](https://support.microsoft.com/kb/3031135).<br/><br/> Zorg ervoor dat er geen Windows-updates in behandeling zijn op de virtuele machine wanneer u een failover triggert. Windows Update kan worden gestart wanneer u een failover uitvoert. u kunt zich pas aanmelden bij de VM als de update is voltooid.
**Azure-VM met Linux** | On-premises machine vóór een failover | Zorg ervoor dat de Secure shell-service op de VM is ingesteld om automatisch te starten bij het opstarten van het systeem.<br/><br/> Controleer of er in de firewallregels is ingesteld dat SSH-verbindingen zijn toegestaan.


## <a name="run-a-failover"></a>Een failover uitvoeren

In deze procedure wordt beschreven hoe u een failover uitvoert voor een [herstel plan](site-recovery-create-recovery-plans.md). Als u een failover wilt uitvoeren voor één virtuele machine, volgt u de instructies voor een virtuele [VMWare-machine](vmware-azure-tutorial-failover-failback.md), een [fysieke server](physical-to-azure-failover-failback.md)of een [Hyper-V-VM](hyper-v-azure-failover-failback-tutorial.md).


Voer de failover van het herstel plan als volgt uit:

1. Selecteer in de site Recovery kluis **herstel plannen**  >  *recoveryplan_name*.
2. Klik op **failover**.

    ![Scherm opname van Azure Site Recovery weer geven van het deel venster ADRP met failover geselecteerd in het menu meer.](./media/site-recovery-failover/Failover.png)

3.   >  Laat in de **richting** van de failover-failover de standaard waarde weg als u naar Azure repliceert.
4. Selecteer in **failover** een **herstel punt** waarvoor u een failover wilt uitvoeren.

    - **Meest recent**: gebruik het meest recente punt. Hiermee worden alle gegevens verwerkt die naar Site Recovery service zijn verzonden en wordt voor elke machine een herstel punt gemaakt. Deze optie biedt de laagste RPO (beoogd herstel punt), omdat de VM die is gemaakt na een failover, alle gegevens bevat die naar Site Recovery worden gerepliceerd wanneer de failover werd geactiveerd.
    Houd er rekening mee dat wanneer de bron regio uitvalt, er geen logboek verwerking meer mogelijk is. Daarom moet u een failover naar het meest recente verwerkte herstel punt. Zie het volgende punt voor meer informatie.
   - **Laatst verwerkte**: gebruik deze optie om virtuele machines over te zetten naar het meest recente herstel punt dat al is verwerkt door site Recovery. U ziet het meest recente verwerkte herstel punt in de **laatste herstel punten** van de virtuele machine. Deze optie biedt een lage RTO omdat er geen tijd wordt besteed aan het verwerken van de niet-verwerkte gegevens
   - **Nieuwste app-consistent**: gebruik deze optie om virtuele machines uit te zetten naar het nieuwste toepassings consistente herstel punt dat is verwerkt door site Recovery.
   - **Laatste verwerkte multi-VM**: met deze optie-vm's die deel uitmaken van een failover van een replicatie groep naar het meest recente, veelvoorkomende herstel punt voor meerdere vm's. Voor andere virtuele machines wordt een failover uitgevoerd naar het meest recente verwerkte herstel punt. Deze optie is alleen voor herstel plannen waarvoor ten minste één virtuele machine met consistentie tussen meerdere VM'S is ingeschakeld.
   - **Nieuwste multi-VM-app-consistent**: met deze optie-vm's die deel uitmaken van een replicatie groep, wordt een failover uitgevoerd naar het meest recente, veelvoorkomende multi-VM-toepassings consistente herstel punt. Andere failover van virtuele machines naar het meest recente toepassings consistente herstel punt. Alleen voor herstel plannen waarvoor ten minste één virtuele machine met consistentie tussen meerdere VM'S is ingeschakeld.
   - **Aangepast**: niet beschikbaar voor herstel plannen. Deze optie is alleen voor failover van afzonderlijke Vm's.

5. Selecteer **afsluit machine voordat u de failover start** als u wilt dat site Recovery bron-vm's afsluit voordat de failover wordt gestart. De failover wordt voortgezet zelfs als het afsluiten is mislukt.  

    > [!NOTE]
    > Als u een failover uitvoert voor Hyper-V-Vm's, probeert afsluiten de on-premises gegevens te synchroniseren en te repliceren die nog niet naar de service zijn verzonden, voordat de failover wordt geactiveerd. 

6. Volg de voortgang van de failover op de pagina **taken** . Zelfs als er fouten optreden, wordt het herstel plan uitgevoerd totdat het is voltooid.
7. Meld u na de failover aan bij de virtuele machine om deze te valideren. 
8. Als u wilt overschakelen naar een ander herstel punt dat voor de failover moet worden gebruikt, gebruikt u **herstel punt wijzigen**.
9. Wanneer u klaar bent, kunt u de failover door voeren. Met de actie **door voeren** worden alle herstel punten verwijderd die beschikbaar zijn bij de service. De optie **herstel punt wijzigen** is niet meer beschikbaar.

## <a name="run-a-planned-failover-hyper-v"></a>Een geplande failover uitvoeren (Hyper-V)

U kunt een geplande failover uitvoeren voor virtuele Hyper-V-machines.

- Een geplande failover is een failover-optie van geen gegevens verlies.
- Wanneer een geplande failover wordt geactiveerd, worden de virtuele bron machines afgesloten. de meest recente gegevens worden gesynchroniseerd en vervolgens wordt een failover geactiveerd.
- U voert een geplande failover uit met de optie voor de **geplande failover** . De oplossing wordt op ongeveer dezelfde manier uitgevoerd als een regel matige failover.
 
## <a name="track-failovers"></a>Failovers bijhouden

Er zijn een aantal taken gekoppeld aan failover.

![Scherm afbeelding van de pagina taken met een lijst met taken met groep 1: begin (1) uitgebreid in de kolom naam. De regel voor de SQLServer-taak is gemarkeerd.](./media/site-recovery-failover/FailoverJob.png)

- **Controle van vereisten**: Hiermee wordt gecontroleerd of aan alle voor waarden die zijn vereist voor de failover is voldaan.
- **Failover**: Hiermee worden de gegevens verwerkt zodat er een virtuele machine van Azure kan worden gemaakt. Als u het **meest recente** herstel punt hebt gekozen, wordt een herstel punt gemaakt op basis van de gegevens die naar de service zijn verzonden.
- **Start**: maakt een Azure VM met behulp van de gegevens die in de vorige stap zijn verwerkt.

> [!WARNING]
> **Een failover in voortgang niet annuleren**: voordat de failover wordt gestart, is replicatie s gestopt voor de virtuele machine. Als u een taak die momenteel wordt uitgevoerd annuleert, stopt de failover, maar wordt de VM niet gerepliceerd. De replicatie kan niet opnieuw worden gestart.


### <a name="extra-failover-time"></a>Extra failover-tijd

In sommige gevallen vereist VM-failover een tussenliggende stap die doorgaans ongeveer acht tot 10 minuten duurt. Dit zijn de computers waarop deze extra stap/tijd van toepassing is:

* Virtuele VMware-machines met een Mobility Service-versie die ouder is dan 9,8.
* Fysieke servers en virtuele Hyper-V-machines die worden beveiligd als fysieke servers.
* VMware Linux-VMs.
* VMware-Vm's waarop deze Stuur Programma's niet aanwezig zijn als opstart Stuur Programma's:
    * storvsc
    * vmbus
    * storflt
    * Intelide
    * ATAPI
* VMware-Vm's waarvoor geen DHCP is ingeschakeld, ongeacht of ze DHCP of statische IP-adressen gebruiken.


## <a name="automate-actions-during-failover"></a>Acties automatiseren tijdens failover

Mogelijk wilt u acties automatiseren tijdens failover. Hiervoor kunt u scripts of Azure Automation-runbooks gebruiken in herstel plannen.

- [Meer informatie](site-recovery-create-recovery-plans.md) over het maken en aanpassen van herstel plannen, inclusief het toevoegen van scripts.
- [Meer informatie](site-recovery-runbook-automation.md) over het toevoegen van Azure Automation runbooks aan herstel plannen.


## <a name="configure-settings-after-failover"></a>Instellingen configureren na failover

### <a name="retain-drive-letters-after-failover"></a>Stationsletters behouden na failover

Site Recovery wordt het bewaren van stationsletters afhandelen. Als u schijven uitsluit tijdens de replicatie van de virtuele machine, kunt u [een voor beeld bekijken](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) van hoe dit werkt.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Voorbereiden in azure om verbinding te maken na een failover

Als u verbinding wilt maken met virtuele Azure-machines die na een failover worden gemaakt met behulp van RDP of SSH, volgt u de vereisten in de tabel.

**Failover** | **Locatie** | **Acties**
--- | --- | ---
**Virtuele Azure-machine met Windows** | Virtuele Azure-machine na een failover |  [Voeg een openbaar IP-adres toe](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) voor de VM.<br/><br/> De regels voor de netwerk beveiligings groep op de virtuele machine waarvoor een failover is uitgevoerd (en het Azure-subnet waarmee deze is verbonden) moeten binnenkomende verbindingen met de RDP-poort toestaan.<br/><br/> Controleer de **Diagnostische gegevens over opstarten** om een scherm opname van de virtuele machine te controleren.<br/><br/> Als u geen verbinding kunt maken, controleert u of de virtuele machine wordt uitgevoerd en raadpleegt u deze [Tips voor probleem oplossing](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure-VM met Linux** | Virtuele Azure-machine na een failover | De regels voor de netwerk beveiligings groep op de virtuele machine waarvoor een failover is uitgevoerd (en het Azure-subnet waarmee deze is verbonden) moeten binnenkomende verbindingen met de SSH-poort toestaan.<br/><br/> [Voeg een openbaar IP-adres toe](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) voor de VM.<br/><br/> Controleer de **Diagnostische gegevens over opstarten** voor een scherm opname van de virtuele machine.<br/><br/>

Volg de stappen die [hier](site-recovery-failover-to-azure-troubleshoot.md) worden beschreven om eventuele verbindingsproblemen na een failover op te lossen.

## <a name="set-up-ip-addressing"></a>IP-adres Sering instellen

- **Interne IP-adressen**: als u het interne IP-adres van een virtuele Azure-machine na een failover wilt instellen, hebt u een aantal opties:
    - Hetzelfde IP-adres behouden: u kunt hetzelfde IP-adres gebruiken op de Azure-VM als de virtuele machine die is toegewezen aan de on-premises computer.
    - Gebruik een ander IP-adres: u kunt een ander IP-adres gebruiken voor de Azure-VM.
    - [Meer informatie](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) over het instellen van interne IP-adressen.
- **Externe IP-adressen**: u kunt open bare IP-adressen behouden bij een failover. Voor virtuele Azure-machines die zijn gemaakt als onderdeel van het failover-proces moet een openbaar IP-adres van Azure worden toegewezen dat beschikbaar is in de Azure-regio. U kunt een openbaar IP-adres hand matig toewijzen of door het proces te automatiseren met een herstel plan. [Meer informatie](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Volgende stappen

Nadat u een failover hebt uitgevoerd, moet u opnieuw beveiligen om de virtuele Azure-machines terug te repliceren naar de on-premises site. Nadat de replicatie is uitgevoerd, kunt u on-premises failback uitvoeren wanneer u klaar bent.

- [Meer informatie](failover-failback-overview.md#reprotectionfailback) over het opnieuw beveiligen en failback.
- [Bereid](vmware-azure-reprotect.md) u voor op de beveiliging van VMware en failback.
- [Failback](hyper-v-azure-failback.md) Virtuele Hyper-V-machines.
- [Meer informatie over](physical-to-azure-failover-failback.md) het proces voor failover en failback voor fysieke servers.