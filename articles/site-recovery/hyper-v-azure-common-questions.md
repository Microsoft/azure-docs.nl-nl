---
title: Veelgestelde vragen over herstel na nood gevallen voor Hyper-V met Azure Site Recovery
description: In dit artikel vindt u een overzicht van algemene vragen over het instellen van herstel na nood gevallen voor on-premises virtuele Hyper-V-machines naar Azure met behulp van de Azure Site Recovery-site.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: e4f5ee71fc56cad247b57f94bf08a68bed8775f9
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754348"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Algemene vragen - Herstel na noodgevallen van Hyper-V naar Azure

In dit artikel vindt u antwoorden op veelgestelde vragen die worden weer gegeven bij het repliceren van on-premises virtuele Hyper-V-machines naar Azure. 

## <a name="general"></a>Algemeen

### <a name="how-is-site-recovery-priced"></a>Hoe wordt Site Recovery geprijsd?
Bekijk de [Azure site Recovery prijs](https://azure.microsoft.com/pricing/details/site-recovery/) gegevens.

### <a name="how-do-i-pay-for-azure-vms"></a>Hoe kan ik betalen voor Azure-Vm's?
Tijdens de replicatie worden gegevens gerepliceerd naar Azure Storage en worden er geen wijzigingen in de virtuele machine betaald. Wanneer u een failover naar Azure uitvoert, maakt Site Recovery automatisch virtuele Azure IaaS-machines. Daarna worden er kosten in rekening gebracht voor de reken bronnen die u gebruikt in Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Zijn er kosten verbonden aan het repliceren van Algemeen v2-opslag account?

Normaal gesp roken ziet u een toename van de kosten voor trans acties die zijn gemaakt voor GPv2-opslag accounts, omdat Azure Site Recovery trans acties zwaar is. [Meer informatie](../storage/common/storage-account-upgrade.md#pricing-and-billing) over het schatten van de wijziging.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Wat heb ik nodig in Hyper-V om replicatie met Site Recovery te organiseren?

Wat u voor de Hyper-V-hostserver nodig hebt, is afhankelijk van het implementatiescenario. Controleer de lijst met vereisten voor Hyper-V in:

* [Replicating Hyper-V VMs (without VMM) to Azure (Virtuele Hyper-V-machines (zonder VMM) repliceren naar Azure)](./hyper-v-azure-tutorial.md)
* [Replicating Hyper-V VMs (with VMM) to Azure (Virtuele Hyper-V-machines (met VMM) repliceren naar Azure)](./hyper-v-vmm-disaster-recovery.md)
* [Replicating Hyper-V VMs to a secondary datacenter (Virtuele Hyper-V-machines repliceren naar een secundair datacenter)](./hyper-v-vmm-disaster-recovery.md)
* Als u repliceert naar een secundair Data Center, leest u over [Ondersteunde gast besturingssystemen voor virtuele Hyper-V-machines](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/mt126277(v=ws.11)).
* Als u repliceert naar Azure, ondersteunt Site Recovery alle gast besturingssystemen die worden [ondersteund door Azure](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan ik Vm's beveiligen wanneer Hyper-V wordt uitgevoerd op een besturings systeem van de client?
Nee, virtuele machines moeten zich bevinden op een Hyper-V-hostserver die wordt uitgevoerd op een ondersteunde Windows-servercomputer. Als u een client computer moet beveiligen, kunt u deze als fysieke machine repliceren naar [Azure](./vmware-azure-tutorial.md) of een [secundair Data Center](./vmware-physical-secondary-disaster-recovery.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Moeten Hyper-V-hosts zich in VMM-Clouds bevindt?
Als u naar een secundair Data Center wilt repliceren, moeten virtuele Hyper-V-machines zich op Hyper-V-hosts in een VMM-cloud bevinden. Als u naar Azure wilt repliceren, kunt u Vm's repliceren met of zonder VMM-Clouds. [Meer](./hyper-v-azure-tutorial.md) informatie over Hyper-V-replicatie naar Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan ik virtuele machines van de tweede generatie Hyper-V repliceren naar Azure?
Ja. Site Recovery wordt van generatie 2 geconverteerd naar generatie 1 tijdens failover. Bij het uitvoeren van een failback wordt de machine teruggeconverteerd naar generatie 2. [Meer informatie](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kan ik Site Recovery met VMM implementeren als ik maar één VMM-server heb?

Ja. U kunt virtuele machines in Hyper-V-servers in de VMM-Cloud repliceren naar Azure, of u kunt repliceren tussen VMM-Clouds op dezelfde server. Voor on-premises naar on-premises replicatie wordt u aangeraden een VMM-server te hebben op zowel de primaire als de secundaire site. 

### <a name="what-do-i-need-in-azure"></a>Wat heb ik nodig in azure?
U hebt een Azure-abonnement, een Recovery Services kluis, een opslag account en een virtueel netwerk nodig. De kluis, het opslag account en het netwerk moeten zich in dezelfde regio bevinden.

### <a name="what-azure-storage-account-do-i-need"></a>Welk Azure Storage-account heb ik nodig?
U hebt een LRS-of GRS-opslag account nodig. GRS wordt aanbevolen, omdat de gegevens dan flexibel zijn te gebruiken als er sprake is van regionale uitval of als de primaire regio niet kan worden hersteld. Premium-opslag wordt ondersteund.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Heeft mijn Azure-account machtigingen nodig om Vm's te maken?
Als u een abonnements beheerder bent, hebt u de benodigde replicatie machtigingen. Als dat niet het geval is, hebt u machtigingen nodig voor het maken van een Azure VM in de resource groep en het virtuele netwerk dat u opgeeft wanneer u Site Recovery en machtigingen voor schrijven naar het geselecteerde opslag account hebt geconfigureerd. [Meer informatie](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Worden er replicatie gegevens verzonden naar Site Recovery?
Nee, Site Recovery worden geen gerepliceerde gegevens onderschept en bevatten geen informatie over wat er op uw Vm's wordt uitgevoerd. Replicatie gegevens worden uitgewisseld tussen Hyper-V-hosts en Azure Storage. Met Site Recovery kunnen deze gegevens niet worden onderschept. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.  

Site Recovery is ISO 27001:2013, 27018, HIPAA, DPA gecertificeerd en is het proces van SOC2-en FedRAMP JAB-evaluaties.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Kunnen we lokale meta gegevens binnen een geografische regio blijven gebruiken?
Ja. Wanneer u een kluis in een regio maakt, zorgen we ervoor dat alle meta gegevens die door Site Recovery worden gebruikt binnen de geografische grens van die regio blijven.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?
Ja, zowel versleuteling als transit en [versleuteling in azure](../storage/common/storage-service-encryption.md) worden ondersteund.


## <a name="deployment"></a>Implementatie

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Wat kan ik doen met replicatie van Hyper-V naar Azure?

- **Herstel na nood** geval: u kunt volledige nood herstel instellen. In dit scenario repliceert u on-premises virtuele Hyper-V-machines naar Azure Storage:
    - U kunt Vm's repliceren naar Azure. Als uw on-premises infra structuur niet beschikbaar is, kunt u een failover uitvoeren naar Azure.
    - Wanneer u een failover hebt uitgevoerd, worden er Azure-Vm's gemaakt met de gerepliceerde gegevens. U hebt toegang tot apps en werk belastingen op de virtuele machines van Azure.
    - Als uw on-premises Data Center weer beschikbaar is, kunt u een failback uitvoeren van Azure naar uw on-premises site.
- **Migratie**: u kunt site Recovery gebruiken om on-premises virtuele Hyper-V-machines te migreren naar Azure Storage. Vervolgens voert u een failover uit van on-premises naar Azure. Na een failover zijn uw apps en workloads beschikbaar en worden deze uitgevoerd op virtuele Azure-machines.


### <a name="what-do-i-need-on-premises"></a>Wat heb ik on-premises nodig?

U hebt een of meer virtuele machines nodig die worden uitgevoerd op een of meer zelfstandige Hyper-V-hosts. U kunt ook Vm's repliceren die worden uitgevoerd op hosts die worden beheerd door System Center Virtual Machine Manager (VMM).
- Als u VMM niet uitvoert, verzamelt u tijdens Site Recovery-implementatie Hyper-V-hosts en-clusters in Hyper-V-sites. U installeert de Site Recovery agents (Azure Site Recovery provider en Recovery Services agent) op elke Hyper-V-host.
- Als Hyper-V-hosts zich in een VMM-cloud bevinden, kunt u de replicatie in VMM organiseren. U installeert de Site Recovery provider op de VMM-server en de Recovery Services-agent op elke Hyper-V-host. U wijst toe tussen VMM logische/VM-netwerken en Azure VNets.
- Meer [informatie](hyper-v-azure-architecture.md) over Hyper-V naar Azure-architectuur.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Kan ik Vm's die zich op een Hyper-V-cluster bevinden, repliceren?

Ja, Site Recovery ondersteunt geclusterde Hyper-V-hosts. Opmerking:

- Alle knoop punten van het cluster moeten worden geregistreerd bij dezelfde kluis.
- Als u VMM niet gebruikt, moeten alle Hyper-V-hosts in het cluster worden toegevoegd aan dezelfde Hyper-V-site.
- U installeert de Azure Site Recovery provider en Recovery Services agent op elke Hyper-V-host in het cluster en voegt elke host toe aan een Hyper-V-site.
- Er hoeven geen specifieke stappen te worden uitgevoerd op het cluster.
- Als u het Deployment Planner-hulp programma voor Hyper-V uitvoert, verzamelt het hulp programma de profiel gegevens van het knoop punt waarop wordt uitgevoerd en waarop de virtuele machine wordt uitgevoerd. Het hulp programma kan geen gegevens verzamelen van een knoop punt dat is uitgeschakeld, maar het knoop punt wordt gevolgd. Nadat het knoop punt actief is, start het hulp programma het verzamelen van de gegevens van het VM-profiel. (als de virtuele machine deel uitmaakt van de lijst met profiel-VM'S en op het knoop punt wordt uitgevoerd).
- Als een virtuele machine op een Hyper-V-host in een Site Recovery kluis naar een andere Hyper-V-host in hetzelfde cluster of naar een zelfstandige host wordt gemigreerd, heeft dit geen invloed op de replicatie voor de virtuele machine. De Hyper-V-host moet voldoen aan [vereisten](hyper-v-azure-support-matrix.md#on-premises-servers)en worden geconfigureerd in een site Recovery kluis. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan ik Vm's beveiligen wanneer Hyper-V wordt uitgevoerd op een besturings systeem van de client?
Nee, virtuele machines moeten zich bevinden op een Hyper-V-hostserver die wordt uitgevoerd op een ondersteunde Windows-servercomputer. Als u een client computer moet beveiligen, kunt u [deze als een fysieke machine](physical-azure-disaster-recovery.md) naar Azure repliceren.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan ik virtuele machines van de tweede generatie Hyper-V repliceren naar Azure?
Ja. Site Recovery wordt van generatie 2 geconverteerd naar generatie 1 tijdens failover. Bij het uitvoeren van een failback wordt de machine teruggeconverteerd naar generatie 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan ik Site Recovery scenario's automatiseren met een SDK?
Ja. U kunt Site Recovery-werkstromen automatiseren met de Rest API-, PowerShell- of Azure-SDK. Momenteel ondersteunde scenario's voor het repliceren van Hyper-V naar Azure met behulp van Power shell:

- [Hyper-V zonder VMM repliceren met Power shell](hyper-v-azure-powershell-resource-manager.md)
- [Hyper-V repliceren met VMM met behulp van Power shell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replicatie

### <a name="where-do-on-premises-vms-replicate-to"></a>Waar worden lokale Vm's gerepliceerd naar?
Gegevens worden gerepliceerd naar Azure Storage. Wanneer u een failover uitvoert, maakt Site Recovery automatisch Azure-Vm's van het opslag account.

### <a name="what-apps-can-i-replicate"></a>Welke apps kan ik repliceren?
U kunt elke app of workload met een Hyper-V-VM die voldoet aan de [replicatie vereisten](hyper-v-azure-support-matrix.md#replicated-vms)repliceren. Site Recovery biedt ondersteuning voor toepassings bewuste replicatie, zodat er failover kan worden uitgevoerd voor apps en een intelligente status kan worden hersteld. Site Recovery integreert met micro soft-toepassingen, zoals share point, Exchange, Dynamics, SQL Server en Active Directory, en werkt nauw samen met toonaangevende leveranciers, waaronder Oracle, SAP, IBM en Red Hat. Lees [hier](site-recovery-workload.md) meer informatie over workloadbeveiliging.

### <a name="whats-the-replication-process"></a>Wat is het replicatie proces?

1. Wanneer de initiële replicatie wordt geactiveerd, wordt een Hyper-V-VM-moment opname gemaakt.
2. Virtuele harde schijven op de virtuele machine worden een voor een gerepliceerd tot ze allemaal zijn gekopieerd naar Azure. Dit kan enige tijd duren, afhankelijk van de grootte van de virtuele machine en de netwerk bandbreedte. Meer informatie over het verg Roten van de netwerk bandbreedte.
3. Als er wijzigingen in de schijf optreden terwijl de eerste replicatie wordt uitgevoerd, worden de wijzigingen door de Hyper-V replica Replication tracker bijgehouden als Hyper-V-replicatie Logboeken (. HRL). Deze logboek bestanden bevinden zich in dezelfde map als de schijven. Elke schijf heeft een bijbehorend. HRL-bestand dat wordt verzonden naar de secundaire opslag. De momentopname- en logboekbestanden nemen schijfbronnen in beslag terwijl de eerste replicatie wordt uitgevoerd.
4. Als de initiële replicatie is voltooid, wordt de VM-momentopname verwijderd.
5. Eventuele schijf wijzigingen in het logboek worden gesynchroniseerd en samengevoegd met de bovenliggende schijf.
6. Nadat de initiële replicatie is voltooid, wordt de beveiliging van de virtuele-machine taak voltooid. De configuratie van het netwerk en andere instellingen na de replicatie wordt zodanig geconfigureerd dat de virtuele machine wordt beveiligd.
7. In deze fase kunt u de VM-instellingen controleren om er zeker van te zijn dat deze gereed is voor failover. U kunt een nood herstel analyse (testfailover) uitvoeren voor de virtuele machine om te controleren of er een failover wordt uitgevoerd naar verwachting.
8. Na de initiële replicatie begint de replicatie van verschillen, in overeenstemming met het replicatie beleid.
9. Wijzigingen worden vastgelegd. HRL-bestanden. Elke schijf die voor replicatie is geconfigureerd, heeft een bijbehorend .hrl-bestand.
10. Het logboek wordt verzonden naar het opslag account van de klant. Wanneer een logboek wordt door lopen naar Azure, worden de wijzigingen in de primaire schijf bijgehouden in een ander logboek bestand, in dezelfde map.
11. Tijdens de replicatie van zowel de eerste als de Delta kunt u de virtuele machine in de Azure Portal bewaken.

Meer [informatie](hyper-v-azure-architecture.md#replication-process) over het replicatie proces.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan ik repliceren naar Azure met een site-naar-site-VPN?

Site Recovery repliceert gegevens van on-premises naar Azure Storage via een openbaar eind punt of met ExpressRoute micro soft-peering. Replicatie via een site-naar-site-VPN-netwerk wordt niet ondersteund.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan ik repliceren naar Azure met ExpressRoute?

Ja, ExpressRoute kan worden gebruikt om Vm's te repliceren naar Azure. Site Recovery repliceert gegevens naar een Azure Storage account via een openbaar eind punt en u moet [micro soft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) instellen voor site Recovery-replicatie. Nadat Vm's een failover naar een virtueel Azure-netwerk hebben uitgevoerd, kunt u ze openen met behulp van [persoonlijke peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Waarom kan ik niet repliceren via VPN?

Wanneer u naar Azure repliceert, bereikt het replicatie verkeer de open bare eind punten van een Azure Storage-account. Zo kunt u alleen repliceren via het open bare Internet met ExpressRoute (micro soft-peering) en werkt VPN niet. 

### <a name="what-are-the-replicated-vm-requirements"></a>Wat zijn de vereisten voor de replicatie van de virtuele machine?

Voor replicatie moet een Hyper-V-VM worden uitgevoerd op een ondersteund besturings systeem. Daarnaast moet de virtuele machine voldoen aan de vereisten voor virtuele Azure-machines. Meer [informatie vindt](hyper-v-azure-support-matrix.md#replicated-vms) u in de ondersteunings matrix.

### <a name="why-is-an-additional-standard-storage-account-required-if-i-replicate-my-virtual-machine-disks-to-premium-storage"></a>Waarom is een extra standaard-opslag account vereist als ik mijn virtuele-machine schijven repliceer naar Premium Storage?

Wanneer u uw on-premises virtuele machines/fysieke servers naar Premium Storage repliceert, worden alle gegevens op de schijven van de beveiligde computer gerepliceerd naar het Premium-opslag account. Er is een extra standaard opslag account vereist voor het opslaan van replicatie Logboeken. Nadat de eerste fase van het repliceren van de schijf gegevens is voltooid, worden alle wijzigingen in de on-premises schijf gegevens doorlopend bijgehouden en opgeslagen als replicatie Logboeken in dit extra standaard opslag account.

### <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?

Virtuele Hyper-V-machines kunnen elke 30 seconden worden gerepliceerd (met uitzonde ring van Premium-opslag) of 5 minuten.

### <a name="can-azure-site-recovery-and-hyper-v-replica-be-configured-together-on-a-hyper-v-machine"></a>Kunnen Azure Site Recovery en Hyper-V replica samen worden geconfigureerd op een Hyper-V-computer?

Ja, zowel Azure Site Recovery als Hyper-V replica kunnen samen worden geconfigureerd voor een machine. Maar de machine moet worden beveiligd als een fysieke machine en wordt gerepliceerd naar Azure met een configuratie/proces server. Meer informatie over het beveiligen van [fysieke machines.](https://docs.microsoft.com/azure/site-recovery/physical-azure-architecture)

### <a name="can-i-extend-replication"></a>Kan ik replicatie uitbreiden?
Uitgebreide of gekoppelde replicatie wordt niet ondersteund. Deze functie aanvragen in het [Feedback forum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan ik een offline initiële replicatie uitvoeren?
Nee, dit wordt niet ondersteund. Vraag deze functie aan in het [Feedback forum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kan ik schijven uitsluiten?
Ja, u kunt schijven uitsluiten van replicatie. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan ik virtuele machines met dynamische schijven repliceren?
Dynamische schijven kunnen worden gerepliceerd. De schijf met het besturings systeem moet een standaard schijf zijn.



## <a name="security"></a>Beveiliging

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Welke toegang Site Recovery nodig heeft voor Hyper-V-hosts

Site Recovery moet toegang hebben tot Hyper-V-hosts om de Vm's te repliceren die u selecteert. Site Recovery installeert het volgende op Hyper-V-hosts:

- Als u VMM niet uitvoert, worden de Azure Site Recovery provider en Recovery Services agent op elke host geïnstalleerd.
- Als u VMM uitvoert, wordt de Recovery Services-agent op elke host geïnstalleerd. De provider wordt uitgevoerd op de VMM-server.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Wat Site Recovery installeren op virtuele Hyper-V-machines?

Site Recovery installeert geen expliciete installatie op virtuele Hyper-V-machines die zijn ingeschakeld voor replicatie.




## <a name="failover-and-failback"></a>Failover en failback


### <a name="how-do-i-fail-over-to-azure"></a>Hoe kan ik failover naar Azure?

U kunt een geplande of niet-geplande failover uitvoeren van on-premises virtuele Hyper-V-machines naar Azure.

- Als u een geplande failover uitvoert, worden de virtuele bronmachines afgesloten om gegevensverlies te voorkomen.
- U kunt een niet-geplande failover uitvoeren als uw primaire site niet toegankelijk is.
- U kunt een failover van één machine uitvoeren of herstelplannen maken om de failover van meerdere virtuele machines te coördineren.
- Failover bestaat uit twee delen:
    - Nadat de eerste fase van de failover is voltooid, kunt u de gemaakte replica-Vm's in azure zien. U kunt een openbaar IP-adres toewijzen aan de VM, indien nodig.
    - Vervolgens voert u de failover uit om toegang te krijgen tot de workload vanuit de replica-VM van Azure.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hoe kan ik toegang tot Azure-Vm's na een failover?
Na een failover kunt u toegang krijgen tot Azure-Vm's via een beveiligde Internet verbinding, via een site-naar-site-VPN of via Azure ExpressRoute. U moet een aantal dingen voorbereiden om verbinding te kunnen maken. [Meer informatie](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Is failover van gegevens met een failover uitgevoerd?
Azure is ontworpen voor herstelbaarheid. Site Recovery is ontworpen voor failover naar een secundair Azure-Data Center, conform de SLA van Azure. Als er een failover optreedt, zorgen we ervoor dat uw meta gegevens en kluizen binnen dezelfde geografische regio blijven die u voor uw kluis hebt gekozen.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?
[Failover](site-recovery-failover.md) is niet automatisch. U initieert failovers met één klik in de portal of u kunt [Power shell](/powershell/module/az.recoveryservices) gebruiken om een failover te activeren.

### <a name="how-do-i-fail-back"></a>Hoe kan ik failback?

Als uw on-premises infra structuur weer actief is, kunt u een failback uitvoeren. Failback vindt plaats in drie fasen:

1. U kunt een geplande failover van Azure naar de on-premises site starten met een aantal verschillende opties:

    - Downtime minimaliseren: als u deze optie gebruikt Site Recovery worden gegevens gesynchroniseerd voor failover. Er wordt gecontroleerd op gewijzigde gegevens blokken en deze worden gedownload naar de on-premises site, terwijl de virtuele machine van Azure actief blijft, waardoor de uitval tijd wordt geminimaliseerd. Wanneer u hand matig opgeeft dat de failover moet worden voltooid, wordt de Azure-VM afgesloten, worden eventuele laatste Delta wijzigingen gekopieerd en wordt de failover gestart.
    - Volledige down load: met deze optie gegevens worden tijdens de failover gesynchroniseerd. Met deze optie wordt de volledige schijf gedownload. Het is sneller omdat er geen controle sommen worden berekend, maar er is meer uitval tijd. Gebruik deze optie als u de replica-Azure-Vm's enige tijd hebt uitgevoerd of als de on-premises VM is verwijderd.

2. U kunt een failback uitvoeren naar dezelfde virtuele machine of naar een andere virtuele machine. U kunt opgeven dat Site Recovery de virtuele machine moet maken als deze nog niet bestaat.
3. Wanneer de initiële synchronisatie is voltooid, selecteert u om de failover te volt ooien. Nadat deze is voltooid, kunt u zich aanmelden bij de on-premises VM om te controleren of alles werkt zoals verwacht. In de Azure Portal ziet u dat de virtuele machines van Azure zijn gestopt.
4. U voert de failover door en opent de werk belasting opnieuw vanaf de on-premises VM.
5. Nadat de workloads weer zijn hersteld, schakelt u omgekeerde replicatie in, zodat de on-premises Vm's opnieuw naar Azure worden gerepliceerd.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan ik een failback uitvoeren naar een andere locatie?
Ja, als u een failover naar Azure hebt uitgevoerd, kunt u een failback uitvoeren naar een andere locatie als het oorspronkelijke exemplaar niet beschikbaar is. [Meer informatie](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
