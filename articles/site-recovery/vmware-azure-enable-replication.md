---
title: Virtuele VMware-machines inschakelen voor herstel na nood geval met behulp van Azure Site Recovery
description: In dit artikel wordt beschreven hoe u VMware-VM-replicatie inschakelt voor nood herstel met behulp van de Azure Site Recovery-service
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 12/07/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 19a98b5786f35839d84e1e969c29e45e2b5e8dea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98573391"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Replicatie inschakelen naar Azure voor VMware-VM's

In dit artikel wordt beschreven hoe u replicatie van on-premises virtuele VMware-machines (VM) naar Azure inschakelt.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat uw systeem voldoet aan de volgende criteria:

- [Stel uw on-premises bron omgeving in](vmware-azure-set-up-source.md).
- [Stel uw doel omgeving in op Azure](vmware-azure-set-up-target.md).
- [Controleer de vereisten](vmware-physical-azure-support-matrix.md) voordat u begint. Belang rijke aandachtspunten voor:
  - [Ondersteunde besturings systemen](vmware-physical-azure-support-matrix.md#replicated-machines) voor gerepliceerde machines.
  - Ondersteuning voor [opslag/schijf](vmware-physical-azure-support-matrix.md#storage) .
  - [Azure-vereisten](vmware-physical-azure-support-matrix.md#azure-vm-requirements) waaraan on-premises machines moeten voldoen.

### <a name="resolve-common-issues"></a>Veelvoorkomende problemen oplossen

- Elke schijf moet kleiner zijn dan 4 TB.
- De schijf met het besturings systeem moet een standaard schijf zijn, niet een dynamische schijf.
- Voor generatie 2 UEFI-virtuele machines moet de besturingssysteem familie Windows zijn en moet de opstart schijf kleiner zijn dan 300 GB.

## <a name="before-you-start"></a>Voordat u begint

Houd bij het repliceren van virtuele VMware-machines de volgende informatie in acht:

- Uw Azure-gebruikers account moet bepaalde [machtigingen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) hebben om replicatie van een nieuwe virtuele machine naar Azure in te scha kelen.
- VMware-Vm's worden elke 15 minuten gedetecteerd. Het kan 15 minuten of langer duren voordat Vm's worden weer gegeven in de Azure Portal na detectie. Wanneer u een nieuwe vCenter-Server of vSphere-host toevoegt, kan de detectie 15 minuten of langer duren.
- Het kan 15 minuten of langer duren voordat omgevings wijzigingen op de virtuele machine in de portal worden bijgewerkt. Bijvoorbeeld: de VMware-hulpprogram ma's installeren.
- U kunt de laatst gedetecteerde tijd voor virtuele VMware-machines controleren: Raadpleeg het veld **laatste contact persoon bij** op de pagina **configuratie servers** voor de VCenter-server/vSphere-host.
- Als u virtuele machines voor replicatie wilt toevoegen zonder te wachten op de geplande detectie, markeert u de configuratie server (maar klikt u er niet op) en selecteert u **vernieuwen**.
- Wanneer u replicatie inschakelt en de virtuele machine is voor bereid, installeert de proces server automatisch de Azure Site Recovery Mobility-service op de VM.

## <a name="enable-replication"></a>Replicatie inschakelen

Lees de volgende informatie voordat u de stappen in deze sectie uitvoert:

- Azure Site Recovery wordt nu rechtstreeks gerepliceerd naar Managed disks voor alle nieuwe replicaties. De proces server schrijft replicatie logboeken naar een cache-opslag account in de doel regio. Deze logboeken worden gebruikt voor het maken van herstel punten in replica Managed disks met een naam Conventie van `asrseeddisk` .
- Power Shell-ondersteuning voor replicatie naar Managed disks is beschikbaar vanaf [AZ. Recovery Services module versie 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- Op het moment van de failover wordt het door u geselecteerde herstel punt gebruikt voor het maken van de doel-beheerde schijf.
- Vm's die eerder zijn geconfigureerd om te worden gerepliceerd naar doel opslag accounts, worden niet beïnvloed.
- Replicatie naar opslag accounts voor een nieuwe virtuele machine is alleen beschikbaar via een Representational State overdracht (REST) API en Power shell. Gebruik Azure REST API versie 2016-08-10 of 2018-01-10 voor replicatie naar opslag accounts.

Voer de volgende stappen uit om replicatie in te scha kelen:

1. Ga naar **stap 2: toepassings**  >  **bron** repliceren. Nadat u replicatie voor de eerste keer hebt ingeschakeld, selecteert u **+ repliceren** in de kluis om replicatie in te scha kelen voor aanvullende virtuele machines.
1. Selecteer de configuratie server in de **bron** pagina > **bron**.
1. Selecteer bij **computer type** **virtual machines** of **fysieke machines**.
1. Selecteer in **vCenter/vSphere-hypervisor** de vCenter-server waarmee de vSphere-host wordt beheerd, of selecteer de host. Deze instelling is niet relevant als u fysieke computers repliceert.
1. Selecteer de proces server. Als er geen extra proces servers zijn gemaakt, is de inbouw proces server van de configuratie server beschikbaar in de vervolg keuzelijst. De integriteits status van elke proces server wordt aangegeven volgens de aanbevolen limieten en andere para meters. Kies een integriteitsprocesserver. Er kan geen [essentiële](vmware-physical-azure-monitor-process-server.md#process-server-alerts) proces server worden gekozen. U kunt [problemen oplossen en de fouten](vmware-physical-azure-troubleshoot-process-server.md) corrigeren **of** een [scale-out processerver](vmware-azure-set-up-process-server-scale.md) instellen.

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Het venster replicatie bron inschakelen":::

   > [!NOTE]
   > Vanaf [versie 9,24](site-recovery-whats-new.md)worden er aanvullende waarschuwingen geïntroduceerd om de status waarschuwingen van de proces server te verbeteren. Voer een upgrade uit voor de Site Recovery onderdelen naar versie 9,24 of hoger voor alle waarschuwingen die moeten worden gegenereerd.

1. Selecteer bij **doel** het abonnement en de resource groep waarin u de virtuele machines waarvoor een failover is uitgevoerd wilt maken. Kies het implementatie model dat u wilt gebruiken in azure voor de virtuele machines waarvoor een failover is uitgevoerd.
1. Selecteer het Azure-netwerk en-subnet waarmee de Azure-Vm's verbinding maken na een failover. Het netwerk moet zich in dezelfde regio bevinden als de Site Recovery service-kluis.

   Selecteer **nu configureren voor geselecteerde machines** om de netwerk instelling toe te passen op alle virtuele machines die u voor beveiliging selecteert. Selecteer **later configureren** om het Azure-netwerk per virtuele machine te selecteren. Als u geen netwerk hebt, moet u er een maken. Als u een netwerk wilt maken met behulp van Azure Resource Manager, selecteert u **nieuwe maken**. Selecteer een subnet, indien van toepassing, en selecteer vervolgens **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Venster replicatie doel inschakelen":::

1. Voor **virtuele machines**  >  **selecteert u virtuele machines**, selecteert u elke virtuele machine die u wilt repliceren. U kunt alleen virtuele machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**. Als u een bepaalde virtuele machine niet kunt zien of selecteren, Zie [bron machine wordt niet vermeld in de Azure Portal](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) om het probleem op te lossen.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Replicatie inschakelen voor het venster virtuele machines selecteren":::

1. Selecteer voor **Eigenschappen**  >  **eigenschappen configureren** het account dat door de proces server wordt gebruikt om automatisch de site Recovery Mobility-service op de VM te installeren. Kies ook het type doel-beheerde schijf dat moet worden gebruikt voor replicatie op basis van uw gegevens verloop patronen.
1. Standaard worden alle schijven van een bron-VM gerepliceerd. Als u schijven wilt uitsluiten van replicatie, schakelt u het selectie vakje **opnemen** uit voor schijven die u niet wilt repliceren. Selecteer vervolgens **OK**. Later kunt u eventueel extra eigenschappen instellen. Meer [informatie](vmware-azure-exclude-disk.md) over het uitsluiten van schijven.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Het venster Eigenschappen voor replicatie configureren inschakelen":::

1.   >  Controleer of het juiste replicatie beleid is geselecteerd in replicatie-instellingen **replicatie-instellingen configureren**. U kunt de instellingen voor het replicatie beleid wijzigen bij **instellingen**  >  **replicatie beleid**  >  _beleids naam_  >  **Instellingen bewerken**. Wijzigingen die worden toegepast op een beleid, zijn ook van toepassing op replicatie en nieuwe virtuele machines.
1. Als u virtuele machines in een replicatie groep wilt verzamelen, schakelt u **consistentie tussen meerdere vm's** in. Geef een naam op voor de groep en selecteer **OK**.

   > [!NOTE]
   > - Virtuele machines in een replicatie groep worden samen gerepliceerd en hebben gedeelde crash-consistente en toepassings consistente herstel punten wanneer ze een failover uitvoeren.
   > - Verzamel Vm's en fysieke servers samen zodat ze uw workloads spie gelen. Het inschakelen van multi-VM-consistentie kan de prestaties van de werk belasting beïnvloeden. Doe dit alleen als de virtuele machines dezelfde werk belasting uitvoeren en u consistentie nodig hebt.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Replicatie venster inschakelen":::

1. Selecteer **Replicatie inschakelen**. U kunt de voortgang van de taak **beveiliging inschakelen** bij **instellingen**  >  **taken**  >  **site Recovery taken** volgen. Nadat de taak **beveiliging volt ooien** is uitgevoerd, is de virtuele machine klaar voor failover.

## <a name="monitor-initial-replication"></a>De initiële replicatie controleren

Nadat ' replicatie inschakelen ' van het beveiligde item is voltooid, initieert Azure Site Recovery replicatie (synoniem voor synchronisatie) van gegevens van de bron machine naar de doel regio. Tijdens deze periode wordt de replica van de bron schijven gemaakt. Pas nadat de oorspronkelijke schijven zijn gekopieerd, worden de wijzigingen in de Delta kopie gekopieerd naar de doel regio. De tijd die nodig is om de oorspronkelijke schijven te kopiëren is afhankelijk van meerdere para meters, zoals:

- grootte van de schijven van de bron machine
- beschik bare band breedte voor de overdracht van gegevens naar Azure (u kunt de implementatie planner gebruiken om de optimale band breedte te bepalen)
- Server bronnen verwerken, zoals geheugen, vrije schijf ruimte, beschik bare CPU voor cache & de gegevens die zijn ontvangen van beveiligde items verwerken (zorg ervoor dat de proces server [in orde](vmware-physical-azure-monitor-process-server.md#monitor-proactively)is)

Als u de voortgang van de eerste replicatie wilt volgen, gaat u naar de Recovery Services-kluis in Azure Portal-> gerepliceerde items-> controle van de kolom Status van het gerepliceerde item. De status toont het percentage van de initiële replicatie. Als u de status aanwijst, is het aantal overgebrachte gegevens beschikbaar. Wanneer u op status klikt, wordt een contextuele pagina geopend en worden de volgende para meters weer gegeven:

- Laatst vernieuwd om: geeft de laatste tijd aan waarop de replicatie gegevens van de hele machine door de service zijn vernieuwd.
- Percentage voltooid: geeft het percentage van de initiële replicatie aan dat is voltooid voor de virtuele machine
- Totaal aantal overgebrachte gegevens-hoeveelheid verzonden gegevens van VM naar Azure

    :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-state.png" alt-text="status-van-replicatie" lightbox="media/vmware-azure-enable-replication/initial-replication-state.png":::

- Synchronisatie voortgang (voor het bijhouden van Details op schijf niveau)
    - Status van replicatie
      - Als replicatie nog wordt gestart, wordt de status bijgewerkt als ' in wachtrij '. Tijdens de eerste replicatie worden slechts drie schijven tegelijk gerepliceerd. Dit mechanisme wordt gevolgd om beperking te voor komen op de proces server.
      - Nadat de replicatie is gestart, wordt de status bijgewerkt als ' wordt uitgevoerd '.
      - Nadat de initiële replicatie is voltooid, wordt de status als ' voltooid ' gemarkeerd.        
   - Site Recovery leest de oorspronkelijke schijf, brengt gegevens over naar Azure en legt de voortgang vast op schijf niveau. Houd er rekening mee dat Site Recovery de replicatie van de niet-bezette grootte van de schijf overgeslagen en toevoegt aan de voltooide gegevens. De som van de gegevens die worden overgedragen over alle schijven, kan dus niet worden toegevoegd aan het ' totale aantal overgedragen gegevens ' op het VM-niveau.
   - Wanneer u op de informatie ballon op een schijf klikt, kunt u Details verkrijgen over wanneer de replicatie (synoniemen voor synchronisatie) is geactiveerd voor de schijf, gegevens die in de afgelopen 15 minuten naar Azure zijn overgedragen, gevolgd door de laatste vernieuwde tijds tempel. Deze tijds tempel geeft de laatste tijd aan waarop gegevens zijn ontvangen door de Azure-service van de initiële replicatie van de bron machine :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-info-balloon.png" alt-text="-info-gegevens ballon-Details" lightbox="media/vmware-azure-enable-replication/initial-replication-info-balloon.png":::
   - De status van elke schijf wordt weer gegeven
      - Als de replicatie langzamer is dan verwacht, verandert de schijf status in waarschuwing
      - Als de replicatie niet wordt uitgevoerd, verandert de schijf status in kritiek

Als de status kritiek/waarschuwing is, controleert u of de replicatie status van de computer en de [proces server](vmware-physical-azure-monitor-process-server.md) in orde zijn. 

Zodra het inschakelen van de replicatie taak is voltooid, zou de voortgang van de replicatie 0% zijn en worden de totale overgedragen gegevens NA. Wanneer u op klikt, zijn de gegevens voor elke geïdentificeerde schijf als ' N.V.T. '. Dit geeft aan dat de replicatie nog moet worden gestart en dat Azure Site Recovery nog de meest recente statistieken moet ontvangen. De voortgang wordt vernieuwd met een interval van 30 minuten.

> [!NOTE]
> Zorg ervoor dat u configuratie servers, scale-out proces servers en mobiliteits agenten bijwerkt naar versie 9,36 of hoger om ervoor te zorgen dat nauw keurige voortgang wordt vastgelegd en verzonden naar Site Recovery Services.


## <a name="view-and-manage-vm-properties"></a>Eigenschappen van virtuele machines weergeven en beheren

Controleer vervolgens de eigenschappen van de virtuele bron machine. Houd er rekening mee dat de Azure VM-naam moet voldoen aan de vereisten van de [virtuele Azure-machine](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Ga naar **instellingen**  >  **gerepliceerde items** en selecteer de virtuele machine. Op de pagina **Essentials** vindt u informatie over de instellingen en status van de virtuele machine.
1. In **Eigenschappen** kunt u de replicatie-en failover-informatie voor de virtuele machine weer geven.
1. In **Compute-en Network**  >  **compute properties** kunt u meerdere VM-eigenschappen wijzigen.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Venster Eigenschappen van Compute en netwerk":::

   - **Azure VM-naam**: Wijzig de naam zo nodig om aan de vereisten van Azure te voldoen.
   - **Grootte van de doel-VM of het VM-type**: de standaard grootte voor de virtuele machine wordt gekozen op basis van para meters die het aantal schijven, het aantal nic's, het CPU-aantal, het geheugen en de beschik bare VM-rollen in de Azure-doel regio bevatten. Azure Site Recovery kiest de eerste beschik bare VM-grootte die voldoet aan alle criteria. U kunt op elk gewenst moment een andere VM-grootte selecteren op basis van uw behoeften voordat de failover wordt uitgevoerd. De grootte van de VM-schijf is ook gebaseerd op de grootte van de bron schijf en kan alleen worden gewijzigd na een failover. Meer informatie over schijf grootten en IOPS-tarieven bij [schaal baarheid en prestatie doelen voor VM-schijven](../virtual-machines/disks-scalability-targets.md).
   - **Resource groep**: u kunt een [resource groep](../azure-resource-manager/management/overview.md#resource-groups)selecteren, van waaruit een virtuele machine deel wordt van een post-failover. U kunt deze instelling op elk gewenst moment wijzigen vóór de failover. Als u na de failover een migratie van de virtuele machine naar een andere resource groep hebt, worden de beveiligings instellingen voor die virtuele machine verbroken.
   - **Beschikbaarheidsset**: u kunt een [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md) selecteren als de virtuele machine deel moet uitmaken van een post-failover. Wanneer u een beschikbaarheidsset selecteert, houdt u de volgende informatie in acht:
     - Alleen beschikbaarheids sets die bij de opgegeven resource groep horen, worden weer gegeven.
     - Vm's die zich op verschillende virtuele netwerken bevinden, kunnen geen deel uitmaken van dezelfde beschikbaarheidsset.
     - Alleen virtuele machines met dezelfde grootte kunnen deel uitmaken van een beschikbaarheidsset.

1. U kunt ook informatie toevoegen over het doelnet werken, het subnet en het IP-adres dat is toegewezen aan de Azure VM.
1. In **schijven** ziet u het besturings systeem en de gegevens schijven op de virtuele machine die worden gerepliceerd.

### <a name="configure-networks-and-ip-addresses"></a>Netwerken en IP-adressen configureren

U kunt het IP-adres van het doel instellen:

- Als u geen adres opgeeft, wordt DHCP gebruikt voor de virtuele machine waarvoor een failover is uitgevoerd.
- Als u een adres instelt dat niet beschikbaar is op failover, werkt de failover niet.
- Als het adres beschikbaar is in het testfailover, kunt u hetzelfde doel-IP-adres gebruiken voor de testfailover.

Het aantal netwerk adapters wordt bepaald door de grootte die u opgeeft voor de virtuele doel machine, als volgt:

- Als het aantal netwerk adapters op de virtuele bron machine kleiner is dan of gelijk is aan het aantal adapters dat is toegestaan voor de grootte van de doel-VM, heeft het doel hetzelfde aantal adapters als de bron.
- Als het aantal adapters voor de virtuele bron machine groter is dan het aantal dat is toegestaan voor de grootte van de doel-VM, wordt het maximum voor de doel grootte gebruikt. Als een virtuele bron machine bijvoorbeeld twee netwerk adapters heeft en de grootte van de doel-VM vier ondersteunt, heeft de virtuele doel machine twee adapters. Als de bron-VM twee adapters heeft, maar de doel grootte slechts een ondersteunt, heeft de doel-VM slechts één adapter.
- Als de virtuele machine meerdere netwerk adapters heeft, worden deze allemaal verbonden met hetzelfde netwerk. De eerste adapter die wordt weer gegeven in de lijst wordt ook de standaard netwerk adapter in de virtuele machine van Azure.

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Klanten van micro soft-Software Assurance kunnen Azure Hybrid Benefit gebruiken om de licentie kosten voor Windows Server-computers die worden gemigreerd naar Azure, op te slaan. Het voor deel is ook van toepassing op herstel na nood gevallen van Azure. Als u in aanmerking komt, kunt u het voor deel toewijzen aan de virtuele machine die Site Recovery maakt als er een failover plaatsvindt.

1. Ga naar de **computer-en netwerk eigenschappen** van de gerepliceerde virtuele machine.
1. Antwoord wanneer u wordt gevraagd of u een Windows Server-licentie hebt waarmee u in aanmerking komt voor Azure Hybrid Benefit.
1. Bevestig dat u een Windows Server-licentie met Software Assurance hebt die u kunt gebruiken om het voor deel toe te passen op de virtuele machine die tijdens de failover wordt gemaakt.
1. Sla de instellingen voor de gerepliceerde virtuele machine op.

Meer [informatie](https://azure.microsoft.com/pricing/hybrid-benefit/) over Azure Hybrid Benefit.

## <a name="next-steps"></a>Volgende stappen

Nadat de virtuele machine een beveiligde status heeft bereikt, probeert u een [failover](site-recovery-failover.md) om te controleren of uw toepassing wordt weer gegeven in Azure.

- Meer [informatie](site-recovery-manage-registration-and-protection.md) over het opschonen van de registratie-en beveiligings instellingen om replicatie uit te scha kelen.
- Meer [informatie](vmware-azure-disaster-recovery-powershell.md) over het automatiseren van replicatie voor uw virtuele machines met behulp van Power shell.
