---
title: Over failover en failback in Azure Site Recovery
description: Meer informatie over failover en fouten in Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: b900655d6fdf1143d430ac842bfd84eb1dfdf34c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070738"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Over on-premises nood herstel failover/failback

Dit artikel bevat een overzicht van failover en failback tijdens nood herstel van on-premises machines naar Azure met [Azure site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Herstel fasen

Failover en failback in Site Recovery hebben vier fasen:

- **Fase 1: failover van on-premises uitvoeren**: nadat u de replicatie naar Azure hebt ingesteld voor on-premises computers en u de on-premises site uitschakelt, mislukken deze machines dan naar Azure. Na een failover worden virtuele Azure-machines gemaakt op basis van gerepliceerde gegevens.
- **Fase 2: Azure-vm's opnieuw beveiligen**: in azure kunt u de Azure-vm's opnieuw beveiligen, zodat ze weer worden gerepliceerd naar de on-premises site. De on-premises VM (indien beschikbaar) is uitgeschakeld tijdens het opnieuw beveiligen, zodat de consistentie van gegevens wordt gegarandeerd.
- **Fase 3: failover van Azure** uitvoeren: als uw on-premises site opnieuw wordt uitgevoerd, voert u een andere failover uit. deze keer dat u een failback uitvoert naar uw on-premises site. U kunt een failback uitvoeren naar de oorspronkelijke locatie van waaruit u een failover hebt uitgevoerd of naar een andere locatie.
- **Fase 4: on-premises machines opnieuw beveiligen**: Schakel na het uitvoeren van een failback de replicatie van de on-premises machines naar Azure opnieuw in.

## <a name="failover"></a>Failover

U voert een failover uit als onderdeel van uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR).

- Als eerste stap in uw BCDR-strategie repliceert u uw on-premises machines naar Azure op een doorlopende basis. Gebruikers hebben toegang tot werk belastingen en apps die worden uitgevoerd op de on-premises bron machines.
- Als de nood zaak zich voordoet, bijvoorbeeld als er sprake is van een onderbreking on-premises, mislukt de replicatie van machines naar Azure. Virtuele Azure-machines worden gemaakt met de gerepliceerde gegevens.
- Voor bedrijfs continuïteit kunnen gebruikers de toegang tot apps op de Azure-Vm's blijven gebruiken.

Failover is een activiteit in twee fasen:

- **Failover**: de failover waarmee een Azure-VM wordt gemaakt en geopend met het geselecteerde herstel punt.
- **Door voeren**: nadat u een failover hebt gecontroleerd, controleert u de virtuele machine in Azure:
    - Vervolgens kunt u de failover door voeren naar het geselecteerde herstel punt of een ander punt selecteren voor de door voer.
    - Na het door voeren van de failover kan het herstel punt niet meer worden gewijzigd.


## <a name="connect-to-azure-after-failover"></a>Verbinding maken met Azure na een failover

Er zijn een aantal vereisten om verbinding te maken met de virtuele Azure-machines die na een failover zijn gemaakt met behulp van RDP/SSH.

**Failover** | **Locatie** | **Acties**
--- | --- | ---
**Virtuele Azure-machine met Windows** | Op de on-premises computer vóór een failover | **Toegang via internet**: Schakel RDP in. Zorg ervoor dat de TCP-en UDP-regels zijn toegevoegd voor **openbaar** en dat RDP is toegestaan voor alle profielen in **Windows Firewall**  >  **toegestane apps**.<br/><br/> **Toegang via site-naar-site-VPN**: Schakel RDP in op de machine. Controleer of RDP is toegestaan in de **Windows Firewall**  ->  **toegestane apps en functies** voor **domein-en particuliere** netwerken.<br/><br/>  Zorg ervoor dat het SAN-beleid van het besturings systeem is ingesteld op **OnlineAll**. [Meer informatie](https://support.microsoft.com/kb/3031135).<br/><br/> Zorg ervoor dat er geen Windows-updates in behandeling zijn op de virtuele machine wanneer u een failover triggert. Windows Update kan worden gestart wanneer er een failover wordt uitgevoerd, en u kunt zich pas aanmelden bij de VM als er updates zijn uitgevoerd.
**Virtuele Azure-machine met Windows** | Na een failover op de Azure-VM |  [Voeg een openbaar IP-adres toe](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) voor de VM.<br/><br/> De regels voor de netwerk beveiligings groep op de virtuele machine waarvoor een failover is uitgevoerd (en het Azure-subnet waarmee deze is verbonden) moeten binnenkomende verbindingen met de RDP-poort toestaan.<br/><br/> Controleer de **Diagnostische gegevens over opstarten** om een scherm opname van de virtuele machine te controleren. Als u geen verbinding kunt maken, controleert u of de virtuele machine wordt uitgevoerd en raadpleegt u tips voor het [oplossen van problemen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure-VM met Linux** | Op de on-premises computer vóór een failover | Zorg ervoor dat de Secure shell-service op de VM is ingesteld om automatisch te starten bij het opstarten van het systeem.<br/><br/> Controleer of er in de firewallregels is ingesteld dat SSH-verbindingen zijn toegestaan.
**Azure-VM met Linux** | Na een failover op de Azure-VM | De regels voor de netwerk beveiligings groep op de virtuele machine waarvoor een failover is uitgevoerd (en het Azure-subnet waarmee deze is verbonden) moeten binnenkomende verbindingen met de SSH-poort toestaan.<br/><br/> [Voeg een openbaar IP-adres toe](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) voor de VM.<br/><br/> Controleer de **Diagnostische gegevens over opstarten** voor een scherm opname van de virtuele machine.<br/><br/>

## <a name="types-of-failover"></a>Typen failover

Site Recovery biedt verschillende opties voor failover.

**Failover** | **Details** | **Herstel** | **Werkstroom**
--- | --- | --- | ---
**Testfailover** | Wordt gebruikt voor het uitvoeren van een analyse die uw BCDR-strategie valideert, zonder gegevens verlies of downtime.| Hiermee maakt u een kopie van de virtuele machine in azure, zonder dat dit van invloed is op de continue replicatie of in uw productie omgeving. | 1. Voer een testfailover uit op één virtuele machine of op meerdere Vm's in een herstel plan.<br/><br/> 2. Selecteer een herstel punt dat u wilt gebruiken voor de testfailover.<br/><br/> 3. Selecteer een Azure-netwerk waarin de virtuele machine van Azure wordt geplaatst wanneer deze wordt gemaakt na een failover. Het netwerk wordt alleen gebruikt voor de testfailover.<br/><br/> 4. Controleer of de boren naar verwachting werkt. Met Site Recovery worden virtuele machines die in azure zijn gemaakt, automatisch opgeschoond tijdens de analyse.
**Geplande failover-Hyper-V**  | Meestal gebruikt voor geplande uitval tijd.<br/><br/> De bron-Vm's worden afgesloten. De meest recente gegevens worden gesynchroniseerd voordat de failover wordt gestart. | Geen gegevens verlies voor de geplande werk stroom. | 1. het onderhouds venster voor uitval tijd plannen en gebruikers op de hoogte stellen.<br/><br/> 2. gebruikers gerichte apps offline halen.<br/><br/> 3. Start een geplande failover met het laatste herstel punt. De failover wordt niet uitgevoerd als de computer niet wordt afgesloten of als er fouten optreden.<br/><br/> 4. Controleer na de failover of de replica-VM van Azure actief is in Azure.<br/><br/> 5. Voer de failover door om te volt ooien. Met de actie door voeren worden alle herstel punten verwijderd.
**Failover-Hyper-V** | Wordt doorgaans uitgevoerd als er sprake is van een niet-geplande onderbreking of als de primaire site niet beschikbaar is.<br/><br/> Sluit de virtuele machine eventueel af en synchroniseer de laatste wijzigingen voordat u de failover initieert.  | Mini maal gegevens verlies voor apps. | 1. Start uw BCDR-plan. <br/><br/> 2. Start een failover. Geef op of Site Recovery de virtuele machine moet afsluiten en synchroniseer/repliceer de meest recente wijzigingen voordat de failover wordt geactiveerd.<br/><br/> 3. u kunt een failover uitvoeren naar een aantal herstel punt opties, in de onderstaande tabel samenvatten.<br/><br/> Als u de optie voor het afsluiten van de virtuele machine niet inschakelt of als Site Recovery deze niet kunt afsluiten, wordt het laatste herstel punt gebruikt.<br/>De failover wordt uitgevoerd, zelfs als de computer niet kan worden afgesloten.<br/><br/> 4. na een failover controleert u of de replica-VM van Azure actief is in Azure.<br/> Indien nodig kunt u een ander herstel punt selecteren in het Bewaar venster van 24 uur.<br/><br/> 5. Voer de failover door om te volt ooien. Met de actie door voeren worden alle beschik bare herstel punten verwijderd.
**Failover-VMware** | Wordt doorgaans uitgevoerd als er sprake is van een niet-geplande onderbreking of als de primaire site niet beschikbaar is.<br/><br/> Geef desgewenst op dat Site Recovery moet proberen om de virtuele machine af te sluiten en om definitieve wijzigingen te synchroniseren en te repliceren voordat de failover wordt gestart.  | Mini maal gegevens verlies voor apps. | 1. Start uw BCDR-plan. <br/><br/> 2. initieer een failover van Site Recovery. Geef op of Site Recovery moet proberen om het afsluiten van de virtuele machine te activeren en te synchroniseren voordat de failover wordt uitgevoerd.<br/> De failover wordt uitgevoerd, zelfs als de computers niet kunnen worden afgesloten.<br/><br/> 3. Controleer na de failover of de replica-VM van Azure actief is in Azure. <br/>Indien nodig kunt u een ander herstel punt selecteren in het Bewaar venster van 72 uur.<br/><br/> 5. Voer de failover door om te volt ooien. Met de actie door voeren worden alle herstel punten verwijderd.<br/> Voor Windows-VM’s worden met Site Recovery de VMware-hulpprogramma’s uitgeschakeld tijdens de failover.

## <a name="failover-processing"></a>Failover-verwerking

In sommige scenario's vereist de failover extra verwerking die circa acht tot tien minuten duurt. Er kunnen langere failover-tijden voor het testen nodig zijn:

* VMware-VM's met een versie van Mobility-service ouder dan 9.8.
* Fysieke servers.
* VMware Linux-VMs.
* Hyper-V-VM's die worden beveiligd als fysieke servers.
* VMware-VM's waarvoor de DHCP-service niet is ingeschakeld.
* VMware-VM's die niet over de volgende opstartstuurprogramma's beschikken: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opties voor herstel punten

Tijdens een failover kunt u een aantal opties voor herstel punten selecteren.

**Optie** | **Details**
--- | ---
**Laatste (laagste RPO)** | Deze optie biedt de laagste Recovery Point Objective (RPO). Eerst worden alle gegevens verwerkt die naar de Site Recovery-service zijn verzonden, om een herstelpunt voor elke VM te maken voordat er een failover naar wordt uitgevoerd. Voor dit herstelpunt worden alle gegevens gerepliceerd naar Site Recovery wanneer de failover werd geactiveerd.
**Laatst verwerkt**  | Met deze optie wordt een failover uitgevoerd voor virtuele machines naar het laatste herstel punt dat is verwerkt door Site Recovery. Als u het meest recente herstel punt voor een specifieke virtuele machine wilt zien, controleert u de **meest recente herstel punten** in de VM-instellingen. Deze optie heeft een lage RTO (Recovery Time Objective), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
**Nieuwste app-consistent** |  Met deze optie wordt een failover uitgevoerd voor virtuele machines naar het nieuwste toepassings consistente herstel punt dat is verwerkt door Site Recovery, als er toepassings consistente herstel punten zijn ingeschakeld. Controleer het meest recente herstel punt in de VM-instellingen.
**Laatste verwerkte multi-VM** | Deze optie is beschikbaar voor herstel plannen met een of meer Vm's waarvoor multi-VM-consistentie is ingeschakeld. Vm's waarvoor de instelling is ingeschakeld, kunnen failover uitvoeren naar het meest recente, veelvoorkomende herstel punt met meerdere VM'S. Voor alle andere Vm's in het plan wordt een failover uitgevoerd naar het meest recente verwerkte herstel punt.
**Nieuwste multi-VM-app-consistent** |  Deze optie is beschikbaar voor herstel plannen met een of meer Vm's waarvoor multi-VM-consistentie is ingeschakeld. Voor virtuele machines die deel uitmaken van een replicatie groep, wordt een failover uitgevoerd naar het meest recente algemene herstel punt van een toepassings consistente multi-VM. Er wordt een failover uitgevoerd voor andere Vm's naar het nieuwste toepassings consistente herstel punt.
**Aangepast** | Gebruik deze optie voor het uitvoeren van een failover van een specifieke virtuele machine naar een bepaald herstel punt in de tijd. Deze optie is niet beschikbaar voor herstel plannen.

> [!NOTE]
> Herstel punten kunnen niet worden gemigreerd naar een andere Recovery Services kluis.

## <a name="reprotectionfailback"></a>Beveiliging/failback

Na een failover naar Azure bevinden de gerepliceerde Azure-Vm's zich in een niet-beveiligde status.

- Als eerste stap bij het uitvoeren van een failback naar uw on-premises site, moet u de virtuele machines van Azure repliceren naar on-premises. Het proces voor opnieuw beveiligen is afhankelijk van het type machines waarvoor een failover is uitgevoerd.
- Nadat de computers zijn gerepliceerd van Azure naar on-premises, kunt u een failover uitvoeren van Azure naar uw on-premises site.
- Nadat de computers weer on-premises worden uitgevoerd, kunt u replicatie inschakelen zodat ze repliceren naar Azure voor herstel na nood gevallen.

Failback werkt als volgt:

- Een virtuele machine heeft ten minste één herstel punt nodig om een failback uit te kunnen laten. In een herstel plan moeten alle virtuele machines in het plan ten minste één herstel punt hebben.
- Het is raadzaam dat u het **meest recente** herstel punt gebruikt om een failback uit te voeren (dit is een crash-consistent punt).
    - Er is een app-consistent herstel punt optie. In dit geval herstelt één virtuele machine het meest recente beschik bare toepassings consistente herstel punt. Voor een herstel plan met een replicatie groep herstelt elke replicatie groep het algemene beschik bare herstel punt.
    - Toepassings consistente herstel punten kunnen zich achter in de tijd bevinden en er kunnen gegevens verloren gaan.
- Tijdens de failover van Azure naar de on-premises site Site Recovery de virtuele machines van Azure afgesloten. Wanneer u de failover doorvoert, verwijdert Site Recovery de mislukte back-ups van virtuele Azure-machines in Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fysiek opnieuw beveiligen/failback

Voor het opnieuw beveiligen en failback van VMware-machines en fysieke servers van Azure naar on-premises, hebt u een failback-infra structuur nodig en zijn er een aantal vereisten.

- **Tijdelijke proces server in azure**: als u een failback van Azure wilt uitvoeren, stelt u een Azure vm in om te fungeren als proces server voor het afhandelen van replicatie van Azure. U kunt deze virtuele machine verwijderen wanneer de failback is voltooid.
- **VPN-verbinding**: als u een failback wilt uitvoeren, hebt u een VPN-verbinding (of ExpressRoute) van het Azure-netwerk nodig naar de on-premises site.
- **Afzonderlijke hoofddoel server**: de Master doel server die is geïnstalleerd met de configuratie server op de on-premises virtuele VMWare-machine, verwerkt de failback. Als u grote hoeveel heden verkeer wilt herstellen, moet u voor dit doel een afzonderlijke on-premises Master doel server instellen.
- **Failbackbeleid**: als u wilt repliceren naar de on-premises site, hebt u een failbackbeleid nodig. Dit beleid wordt automatisch gemaakt wanneer u een replicatie beleid maakt op basis van on-premises naar Azure.
    - Dit beleid wordt automatisch gekoppeld aan de configuratie server.
    - U kunt dit beleid niet bewerken.
    - Beleids waarden: RPO-drempel waarde-15 minuten; Bewaar periode van het herstel punt: 24 uur; Frequentie van de app-consistente moment opname-60 minuten.

Meer informatie over VMware/fysiek opnieuw beveiligen en failback:
- [Bekijk](vmware-azure-reprotect.md#before-you-begin) aanvullende vereisten voor opnieuw beveiligen en failback.
- [Implementeer](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) een proces server in Azure.
- [Implementeer](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) een afzonderlijke hoofddoel server.

Wanneer u Azure-Vm's opnieuw beveiligt naar on-premises, kunt u opgeven dat u een failback wilt uitvoeren naar de oorspronkelijke locatie of naar een andere locatie.

- **Herstel van de oorspronkelijke locatie**: dit is een failback van Azure naar dezelfde bron on-premises machine als deze bestaat. In dit scenario worden alleen de wijzigingen gerepliceerd naar on-premises.
- **Herstel naar een andere locatie**: als de on-premises machine niet bestaat, kunt u een failback uitvoeren van Azure naar een andere locatie. Wanneer u de Azure-VM opnieuw beveiligt naar on-premises, wordt de on-premises machine gemaakt. Volledige gegevens replicatie vindt plaats van Azure naar on-premises. -- [Bekijk](concepts-types-of-failback.md) de vereisten en beperkingen voor locatie failback.



## <a name="hyper-v-reprotectionfailback"></a>Hyper-V-beveiliging/failback

Virtuele Hyper-V-machines opnieuw beveiligen en failback van Azure naar on-premises:

- U kunt alleen een failback uitvoeren van virtuele Hyper-V-machines die worden gerepliceerd met behulp van een opslag account. Failback van virtuele Hyper-V-machines die worden gerepliceerd met Managed disks, wordt niet ondersteund.
- On-premises Hyper-V-hosts (of System Center VMM indien gebruikt) moeten zijn verbonden met Azure.
- U voert een geplande failback van Azure naar on-premises uit.
- Er hoeven geen specifieke onderdelen te worden ingesteld voor de failback van Hyper-V-VM'S.
- Tijdens een geplande failover kunt u opties selecteren om gegevens te synchroniseren voor de failback:
    - **Gegevens synchroniseren vóór failover**: met deze optie wordt de uitval tijd voor virtuele machines geminimaliseerd wanneer de computers worden gesynchroniseerd zonder dat ze worden afgesloten.
        - Fase 1: Hiermee wordt een moment opname van de virtuele machine van Azure gemaakt en gekopieerd naar de on-premises Hyper-V-host. De machine blijft actief in Azure.
        - Fase 2: Hiermee wordt de virtuele machine van Azure afgesloten, zodat er geen nieuwe wijzigingen worden uitgevoerd. De laatste set Delta wijzigingen wordt overgedragen naar de on-premises server en de on-premises VM wordt gestart.
    - **Gegevens alleen synchroniseren tijdens de failover**: deze optie is sneller omdat we verwachten dat het grootste deel van de schijf is gewijzigd en daarom geen controlesom berekeningen uitvoeren. Hiermee wordt de schijf gedownload. We raden u aan deze optie te gebruiken als de virtuele machine al een tijdje is uitgevoerd in azure, of als de on-premises virtuele machine is verwijderd.

Meer [informatie](hyper-v-azure-failback.md) over Hyper-V-opnieuw beveiligen en failback.

Wanneer u Azure-Vm's opnieuw beveiligt naar on-premises, kunt u opgeven dat u een failback wilt uitvoeren naar de oorspronkelijke locatie of naar een andere locatie.

- **Herstel van de oorspronkelijke locatie**: dit is een failback van Azure naar dezelfde bron on-premises machine als deze bestaat. In dit scenario selecteert u een van de synchronisatie opties die zijn beschreven in de vorige procedure.
- **Herstel naar een andere locatie**: als de on-premises machine niet bestaat, kunt u een failback uitvoeren van Azure naar een andere locatie. Wanneer u de Azure-VM opnieuw beveiligt naar on-premises, wordt de on-premises machine gemaakt. Met deze optie raden wij u aan de optie voor het synchroniseren van gegevens voor de failover te selecteren
- [Bekijk](hyper-v-azure-failback.md) de vereisten en beperkingen voor locatie failback.


Nadat u de on-premises site niet meer hebt gemaakt, schakelt u **achterwaarts repliceren** in om de virtuele machine te repliceren naar Azure, waarbij de cyclus wordt voltooid.




## <a name="next-steps"></a>Volgende stappen
- Failover van [specifieke virtuele VMware-machines](vmware-azure-tutorial-failover-failback.md)
- Failover van [specifieke virtuele Hyper-V-machines](hyper-v-azure-failover-failback-tutorial.md).
- Een herstel plan [maken](site-recovery-create-recovery-plans.md) .
- Failover [van vm's in een herstel plan](site-recovery-failover.md).
- [Voorbereiden voor](vmware-azure-failback.md) VMware-rebeveiliging en failback.
- Failback van [virtuele Hyper-V-machines](hyper-v-azure-failback.md).
