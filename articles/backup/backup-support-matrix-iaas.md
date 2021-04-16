---
title: Ondersteuningsmatrix voor back-up van Azure-VM
description: Biedt een samenvatting van de ondersteuningsinstellingen en beperkingen bij het maken van back-up van virtuele Azure-Azure Backup service.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 1f63d0c3ad448a8ab9b91764d4c369fefddea25d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516719"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Ondersteuningsmatrix voor back-up van Azure-VM

U kunt de Azure Backup [gebruiken om back-up](backup-overview.md) te maken van on-premises machines en workloads, en van virtuele Azure-machines (VM's). In dit artikel vindt u een overzicht van de ondersteuningsinstellingen en beperkingen wanneer u een back-up van Virtuele Azure-Azure Backup.

Andere ondersteunings-matrices:

- [Algemene ondersteuningsmatrix](backup-support-matrix.md) voor Azure Backup
- [Ondersteuningsmatrix](backup-support-matrix-mabs-dpm.md) voor Azure Backup back-up van System Center Data Protection Manager server/server (DPM)
- [Ondersteuningsmatrix](backup-support-matrix-mars-agent.md) voor back-up met Microsoft Azure Recovery Services-agent (MARS)

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Hier ziet u hoe u een back-up kunt maken van virtuele Azure-VM's en deze kunt herstellen met de Azure Backup service.

**Scenario** | **Een back-up maken** | **Agent** |**Herstellen**
--- | --- | --- | ---
Directe back-up van Azure-VM's  | Een back-up maken van de hele VM.  | Er is geen extra agent nodig op de Azure-VM. Azure Backup installeert en gebruikt een extensie voor de [Azure VM-agent](../virtual-machines/extensions/agent-windows.md) die wordt uitgevoerd op de VM. | Herstel als volgt:<br/><br/> - **Maak een eenvoudige VM.** Dit is handig als de VM geen speciale configuratie heeft, zoals meerdere IP-adressen.<br/><br/> - **Herstel de VM-schijf**. Herstel de schijf. Koppel deze vervolgens aan een bestaande VM of maak een nieuwe VM op basis van de schijf met behulp van PowerShell.<br/><br/> - **Vervang de VM-schijf**. Als er een VM bestaat en deze beheerde schijven gebruikt (niet-versleuteld), kunt u een schijf herstellen en deze gebruiken om een bestaande schijf op de VM te vervangen.<br/><br/> - **Herstel specifieke bestanden/mappen.** U kunt bestanden/mappen herstellen vanaf een VM in plaats van vanaf de hele VM.
Directe back-up van Azure-VM's (alleen Windows)  | Een back-up maken van specifieke bestanden/mappen/volume. | Installeer de [Azure Recovery Services-agent.](backup-azure-file-folder-backup-faq.yml)<br/><br/> U kunt de MARS-agent uitvoeren naast de back-upextensie voor de Azure VM-agent om een back-up te maken van de VM op bestands-/mapniveau. | Specifieke mappen/bestanden herstellen.
Een back-up maken van azure-VM naar back-upserver  | Een back-up maken van bestanden/mappen/volumes; systeemtoestand/bare-metalbestanden; app-gegevens naar System Center DPM of Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS maakt vervolgens een back-up naar de back-upkluis. | Installeer de DPM/MABS-beveiligingsagent op de VM. De MARS-agent is geïnstalleerd op DPM/MABS.| Bestanden/mappen/volumes herstellen; systeemtoestand/bare-metalbestanden; app-gegevens.

Meer informatie over back-ups [maken met behulp van een back-upserver](backup-architecture.md#architecture-back-up-to-dpmmabs) en over [ondersteuningsvereisten.](backup-support-matrix-mabs-dpm.md)

## <a name="supported-backup-actions"></a>Ondersteunde back-upacties

**Actie** | **Ondersteuning**
--- | ---
Een back-up maken van een VM die wordt afgesloten/offline VM | Ondersteund.<br/><br/> Momentopname is alleen crash-consistent, niet app-consistent.
Een back-up maken van schijven na de migratie naar beheerde schijven | Ondersteund.<br/><br/> De back-up blijft werken. Geen actie vereist.
Back-up maken van beheerde schijven na het inschakelen van resourcegroepvergrendeling | Wordt niet ondersteund.<br/><br/> Azure Backup oudere herstelpunten kunnen niet worden verwijderd en back-ups mislukken wanneer de maximumlimiet van herstelpunten is bereikt.
Back-upbeleid voor een VM wijzigen | Ondersteund.<br/><br/> Van de VM wordt een back-up gemaakt met behulp van de plannings- en retentie-instellingen in het nieuwe beleid. Als de retentie-instellingen worden uitgebreid, worden bestaande herstelpunten gemarkeerd en bewaard. Als ze worden verminderd, worden bestaande herstelpunten verwijderd in de volgende opschoonactie en uiteindelijk verwijderd.
Een back-up job annuleren| Ondersteund tijdens het momentopnameproces.<br/><br/> Wordt niet ondersteund wanneer de momentopname wordt overgebracht naar de kluis.
Een back-up maken van de VM naar een andere regio of een ander abonnement |Wordt niet ondersteund.<br><br>Als u een back-up wilt maken, moeten virtuele machines zich in hetzelfde abonnement als de kluis voor back-up.
Back-ups per dag (via de Azure VM-extensie) | Eén geplande back-up per dag.<br/><br/>De Azure Backup-service ondersteunt maximaal drie back-ups op aanvraag per dag en één extra geplande back-up.
Back-ups per dag (via de MARS-agent) | Drie geplande back-ups per dag.
Back-ups per dag (via DPM/MABS) | Twee geplande back-ups per dag.
Maandelijkse/jaarlijkse back-up| Niet ondersteund bij het maken van een back-up met de Azure VM-extensie. Alleen dagelijks en wekelijks wordt ondersteund.<br/><br/> U kunt het beleid instellen om dagelijkse/wekelijkse back-ups te bewaren voor een maandelijkse/jaarlijkse bewaarperiode.
Automatische klokaanpassing | Wordt niet ondersteund.<br/><br/> Azure Backup wordt niet automatisch aangepast voor wijzigingen in de zomertijd bij het maken van een back-up van een VM.<br/><br/>  Pas het beleid waar nodig handmatig aan.
[Beveiligingsfuncties voor hybride back-up](./backup-azure-security-feature.md) |Het uitschakelen van beveiligingsfuncties wordt niet ondersteund.
Een back-up maken van de VM waarvan de machinetijd wordt gewijzigd | Wordt niet ondersteund.<br/><br/> Als de tijd van de machine wordt gewijzigd in een toekomstige datum/tijd na het inschakelen van de back-up voor die VM, wordt een geslaagde back-up echter niet gegarandeerd, zelfs als de tijdswijziging wordt teruggedraaid.

## <a name="operating-system-support-windows"></a>Besturingssysteemondersteuning (Windows)

De volgende tabel bevat een overzicht van de ondersteunde besturingssystemen bij het maken van back-up van virtuele Windows Azure-VM's.

**Scenario** | **Ondersteuning voor besturingssysteem**
--- | ---
Back-up maken met azure VM-agentextensie | - Windows 10 Client (alleen 64-bits) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2012 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM en SP1 Standard)  <br/><br/> - Windows Server 2008 (alleen 64-bits)
Back-up maken met MARS-agent | [Ondersteunde](backup-support-matrix-mars-agent.md#supported-operating-systems) besturingssystemen.
Back-up maken met DPM/MABS | Ondersteunde besturingssystemen voor back-up [met MABS](backup-mabs-protection-matrix.md) en [DPM](/system-center/dpm/dpm-protection-matrix).

Azure Backup biedt geen ondersteuning voor 32-bits besturingssystemen.

## <a name="support-for-linux-backup"></a>Ondersteuning voor Linux-back-up

Dit wordt ondersteund als u een back-up wilt maken van Linux-machines.

**Actie** | **Ondersteuning**
--- | ---
Back-up maken van Virtuele Linux Azure-VM's met de Linux Azure VM-agent | Bestands consistente back-up.<br/><br/> App-consistente back-up met behulp van [aangepaste scripts](backup-azure-linux-app-consistent.md).<br/><br/> Tijdens het herstellen kunt u een nieuwe VM maken, een schijf herstellen en deze gebruiken om een VM te maken, of een schijf herstellen en deze gebruiken om een schijf op een bestaande VM te vervangen. U kunt ook afzonderlijke bestanden en mappen herstellen.
Back-up maken van Virtuele Linux-azure-VM's met MARS-agent | Wordt niet ondersteund.<br/><br/> De MARS-agent kan alleen worden geïnstalleerd op Windows-machines.
Een back-up maken van virtuele Linux Azure-VM's met DPM/MABS | Wordt niet ondersteund.
Back-ups maken van Virtuele Linux Azure-VM's met Docker-mount points | Momenteel biedt Azure Backup geen ondersteuning voor uitsluiting van Docker-bevestigingspunten, omdat deze elke keer op verschillende paden worden bevestigd.

## <a name="operating-system-support-linux"></a>Besturingssysteemondersteuning (Linux)

Voor Linux-back-ups van Virtuele Azure-Azure Backup ondersteuning voor de lijst met [Linux-distributies](../virtual-machines/linux/endorsed-distros.md)die zijn goedgekeurd door Azure. Houd rekening met het volgende:

- Azure Backup biedt geen ondersteuning voor Core OS Linux.
- Azure Backup biedt geen ondersteuning voor 32-bits besturingssystemen.
- Andere Bring Your Own Linux-distributies werken mogelijk zolang de [Azure VM-agent](../virtual-machines/extensions/agent-linux.md) voor Linux beschikbaar is op de VM, en zolang Python wordt ondersteund.
- Azure Backup biedt geen ondersteuning voor een linux-VM die via een proxy is geconfigureerd als Python-versie 2.7 niet is geïnstalleerd.
- Azure Backup biedt geen ondersteuning voor het maken van back-up van NFS-bestanden die vanuit de opslag of vanaf een andere NFS-server op Linux- of Windows-computers zijn bevestigd. Er wordt alleen een back-up gemaakt van schijven die lokaal zijn gekoppeld aan de VM.

## <a name="backup-frequency-and-retention"></a>Back-upfrequentie en -retentie

**Instelling** | **Limieten**
--- | ---
Maximum aantal herstelpunten per beveiligd exemplaar (machine/workload) | 9999.
Maximale verlooptijd voor een herstelpunt | Geen limiet (99 jaar).
Maximale back-upfrequentie naar kluis (Azure VM-extensie) | Eenmaal per dag.
Maximale back-upfrequentie naar kluis (MARS-agent) | Drie back-ups per dag.
Maximale back-upfrequentie naar DPM/MABS | Om de 15 minuten SQL Server.<br/><br/> Eenmaal per uur voor andere workloads.
Bewaarperiode van herstelpunt | Dagelijks, wekelijks, maandelijks en jaarlijks.
Maximale bewaarperiode | Afhankelijk van back-upfrequentie.
Herstelpunten op DPM-/MABS-schijf | 64 voor bestandsservers en 448 voor app-servers.<br/><br/> Tapeherstelpunten zijn onbeperkt voor on-premises DPM.

## <a name="supported-restore-methods"></a>Ondersteunde herstelmethoden

**Optie voor herstellen** | **Details**
--- | ---
**Een nieuwe virtuele machine maken** | Hiermee maakt en krijgt u snel een standaard-VM die vanaf een herstelpunt actief is.<br/><br/> U kunt een naam opgeven voor de virtuele machine, de resourcegroep en het virtuele netwerk (VNet) selecteren waarin deze worden geplaatst en een opslagaccount opgeven voor de herstelde VM. De nieuwe VM moet in dezelfde regio worden gemaakt als de bron-VM.
**Schijf herstellen** | Hiermee wordt een VM-schijf hersteld, die vervolgens kan worden gebruikt om een nieuwe virtuele machine te maken.<br/><br/> Azure Backup biedt u een sjabloon waarmee u een nieuwe virtuele machine kunt aanpassen en maken. <br/><br> Met de herstel taak wordt een sjabloon gegenereerd die u kunt downloaden en gebruiken om aangepaste VM-instellingen op te geven en een VM te maken.<br/><br/> De schijven worden gekopieerd naar de resourcegroep die u opgeeft.<br/><br/> U kunt de schijf ook koppelen aan een bestaande VM of een nieuwe VM maken met behulp van PowerShell.<br/><br/> Deze optie is handig als u de virtuele machine wilt aanpassen, configuratie-instellingen wilt toevoegen die niet waren geconfigureerd op het moment van de back-up of instellingen wilt toevoegen die moeten worden geconfigureerd met de sjabloon of PowerShell.
**Bestaande schijf vervangen** | U kunt een schijf herstellen en deze gebruiken om een schijf op de bestaande VM te vervangen.<br/><br/> De huidige VM moet bestaan. Als deze is verwijderd, kan deze optie niet worden gebruikt.<br/><br/> Azure Backup maakt een momentopname van de bestaande VM voordat u de schijf vervangt en slaat deze op de faseringslocatie op die u opgeeft. Bestaande schijven die zijn verbonden met de virtuele machine worden vervangen door het geselecteerde herstelpunt.<br/><br/> De momentopname wordt gekopieerd naar de kluis en bewaard in overeenstemming met het bewaarbeleid. <br/><br/> Na de schijfbewerking wordt de oorspronkelijke schijf bewaard in de resourcegroep. U kunt ervoor kiezen om de oorspronkelijke schijven handmatig te verwijderen als ze niet nodig zijn. <br/><br/>Bestaande vervangen wordt ondersteund voor niet-versleutelde beheerde VM's en voor VM's die [zijn gemaakt met aangepaste afbeeldingen.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Het wordt niet ondersteund voor niet-mande schijven en VM's, klassieke VM's en [ge generaliseerde VM's.](../virtual-machines/windows/capture-image-resource.md)<br/><br/> Als het herstelpunt meer of minder schijven heeft dan de huidige VM, geeft het aantal schijven in het herstelpunt alleen de VM-configuratie weer.<br><br> Bestaande vervangen wordt ook ondersteund voor VM's met gekoppelde resources, zoals door de gebruiker toegewezen [beheerde](../active-directory/managed-identities-azure-resources/overview.md) identiteit en [Key Vault](../key-vault/general/overview.md).
**In meerdere regio's (secundaire regio)** | Herstellen tussen regio's kan worden gebruikt om Azure-VM's in de secundaire regio te herstellen. Dit is een [gekoppelde Azure-regio.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> U kunt alle Azure-VM's voor het geselecteerde herstelpunt herstellen als de back-up wordt uitgevoerd in de secundaire regio.<br><br> Deze functie is beschikbaar voor de volgende opties:<br> <li> [Een VM maken](./backup-azure-arm-restore-vms.md#create-a-vm): <br> <li> [Schijven herstellen](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> De optie Bestaande schijven vervangen wordt [momenteel niet](./backup-azure-arm-restore-vms.md#replace-existing-disks) ondersteund.<br><br> Machtigingen<br> De herstelbewerking in de secundaire regio kan worden uitgevoerd door back-upbeheerders en app-beheerders.

## <a name="support-for-file-level-restore"></a>Ondersteuning voor herstel op bestandsniveau

**Herstellen** | **Ondersteund**
--- | ---
Bestanden herstellen tussen besturingssystemen | U kunt bestanden herstellen op elke computer met hetzelfde (of compatibele) besturingssysteem als de back-up van de VM. Zie de [tabel Compatibel besturingssysteem.](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script)
Bestanden herstellen van versleutelde VM's | Wordt niet ondersteund.
Bestanden herstellen vanuit opslagaccounts met beperkte netwerkverbinding | Wordt niet ondersteund.
Bestanden herstellen op VM's met Windows-opslagruimten | Herstellen wordt niet ondersteund op dezelfde VM.<br/><br/> Herstel in plaats daarvan de bestanden op een compatibele VM.
Bestanden herstellen op linux-VM met behulp van LVM-/raid-matrices | Herstellen wordt niet ondersteund op dezelfde VM.<br/><br/> Herstellen op een compatibele VM.
Bestanden met speciale netwerkinstellingen herstellen | Herstellen wordt niet ondersteund op dezelfde VM. <br/><br/> Herstellen op een compatibele VM.
Bestanden herstellen van gedeelde schijf, tijdelijke schijf, ontdubbelde schijf, Ultra-schijf en schijf met Write Accelerator ingeschakeld | Herstellen wordt niet ondersteund, <br/><br/>zie [Ondersteuning voor Azure VM-opslag.](#vm-storage-support)

## <a name="support-for-vm-management"></a>Ondersteuning voor VM-beheer

De volgende tabel bevat een overzicht van de ondersteuning voor back-ups tijdens VM-beheertaken, zoals het toevoegen of vervangen van VM-schijven.

**Herstellen** | **Ondersteund**
--- | ---
Herstellen in abonnement/regio/zone. | Wordt niet ondersteund.
Herstellen naar een bestaande VM | Gebruik de optie Schijf vervangen.
Schijf herstellen met opslagaccount ingeschakeld voor Azure Storage Service Encryption (SSE) | Wordt niet ondersteund.<br/><br/> Herstellen naar een account waar SSE niet is ingeschakeld.
Herstellen naar gemengde opslagaccounts |Wordt niet ondersteund.<br/><br/> Op basis van het type opslagaccount zijn alle herstelde schijven Premium of Standard en niet gemengd.
VM rechtstreeks herstellen naar een beschikbaarheidsset | Voor beheerde schijven kunt u de schijf herstellen en de beschikbaarheidssetoptie in de sjabloon gebruiken.<br/><br/> Niet ondersteund voor niet-mande schijven. Voor niet-mande schijven herstelt u de schijf en maakt u vervolgens een VM in de beschikbaarheidsset.
Back-up van niet-beheerde VM's herstellen na een upgrade naar een beheerde VM| Ondersteund.<br/><br/> U kunt schijven herstellen en vervolgens een beheerde VM maken.
VM herstellen naar herstelpunt voordat de VM werd gemigreerd naar beheerde schijven | Ondersteund.<br/><br/> U herstelt naar niet-beheerde schijven (standaard), converteert de herstelde schijven naar een beheerde schijf en maakt een VM met de beheerde schijven.
Herstel een VM die is verwijderd. | Ondersteund.<br/><br/> U kunt de VM herstellen vanaf een herstelpunt.
Een domeincontroller-VM herstellen  | Ondersteund. Zie VM's [voor domeincontrollers herstellen voor meer informatie.](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)
VM in een ander virtueel netwerk herstellen |Ondersteund.<br/><br/> Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio.

## <a name="vm-compute-support"></a>Ondersteuning voor VM-rekenkracht

**Compute** | **Ondersteuning**
--- | ---
VM-grootte |Elke Azure-VM-grootte met ten minste 2 CPU-kernen en 1 GB RAM-geheugen.<br/><br/> [Meer informatie.](../virtual-machines/sizes.md)
Back-up maken van VM's in [beschikbaarheidssets](../virtual-machines/availability.md#availability-sets) | Ondersteund.<br/><br/> U kunt een VM in een beschikbare set niet herstellen met behulp van de optie om snel een VM te maken. Wanneer u in plaats daarvan de VM herstelt, herstelt u de schijf en gebruikt u deze voor het implementeren van een VM, of herstelt u een schijf en gebruikt u deze om een bestaande schijf te vervangen.
Back-up maken van VM's die zijn geïmplementeerd met [Hybrid Use Benefit (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Ondersteund.
Back-up maken van VM's die zijn geïmplementeerd [vanuit Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Gepubliceerd door Microsoft, van derden) |Ondersteund.<br/><br/> Op de VM moet een ondersteund besturingssysteem worden uitgevoerd.<br/><br/> Wanneer u bestanden op de VM herstelt, kunt u alleen herstellen naar een compatibel besturingssysteem (niet een eerder of later besturingssysteem). We herstellen de VM'Azure Marketplace als VM's, omdat deze aankoopgegevens nodig hebben. Ze worden alleen hersteld als schijven.
Back-up maken van VM's die zijn geïmplementeerd vanuit een aangepaste afbeelding (van derden) |Ondersteund.<br/><br/> Op de VM moet een ondersteund besturingssysteem worden uitgevoerd.<br/><br/> Wanneer u bestanden herstelt op de VM, kunt u alleen herstellen naar een compatibel besturingssysteem (niet een eerder of later besturingssysteem).
Back-up maken van VM's die naar Azure worden gemigreerd| Ondersteund.<br/><br/> Als u een back-up wilt maken van de VM, moet de VM-agent op de gemigreerde machine zijn geïnstalleerd.
Back-up maken van multi-VM-consistentie | Azure Backup biedt geen gegevens- en toepassingsconsistentie voor meerdere VM's.
Back-up met [diagnostische instellingen](../azure-monitor/essentials/platform-logs-overview.md)  | Unsupported. <br/><br/> Als het herstellen van de Azure-VM met diagnostische instellingen wordt geactiveerd met de optie [Nieuwe](backup-azure-arm-restore-vms.md#create-a-vm) maken, mislukt het herstellen.
Herstellen van zone-vastgemaakte VM's | Ondersteund (voor een VM met een back-up na januari 2019 en waar [beschikbaarheidszones](https://azure.microsoft.com/global-infrastructure/availability-zones/) beschikbaar zijn).<br/><br/>We ondersteunen momenteel het herstellen naar dezelfde zone die is vastgemaakt in VM's. Als de zone echter niet beschikbaar is vanwege een storing, mislukt het herstellen.
Gen2-VM's | Ondersteund <br> Azure Backup biedt ondersteuning voor back-up en herstel van [Gen2-VM's.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) Wanneer deze VM's worden hersteld vanaf het herstelpunt, worden ze hersteld als [Gen2-VM's.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)
Back-up van Azure-VM's met vergrendelingen | Niet ondersteund voor niet-mande VM's. <br><br> Ondersteund voor beheerde VM's.
[Spot-VM's](../virtual-machines/spot-vms.md) | Unsupported. Azure Backup herstelt spot-VM's als gewone Azure-VM's.
[Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) | Ondersteund
Configuratie van zelfstandige Azure-VM's in Windows Storage Spaces | Ondersteund
[Azure VM Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md#scale-sets-with-flexible-orchestration) | Ondersteund voor uniforme en flexibele orchestration-modellen voor het maken van back-up en herstel van één Azure-VM.

## <a name="vm-storage-support"></a>Ondersteuning voor VM-opslag

**Onderdeel** | **Ondersteuning**
--- | ---
Azure VM-gegevensschijven | Ondersteuning voor back-ups van Azure-VM's met maximaal 32 schijven.<br><br> Ondersteuning voor het maken van back-ups van Azure-VM's met niet-mande schijven of klassieke VM's is maximaal 16 schijven.
Grootte van gegevensschijf | De grootte van een afzonderlijke schijf kan maximaal 32 TB zijn en een maximum van 256 TB voor alle schijven in een VM.
Opslagtype | Standard HDD, Standard SSD, Premium SSD.
Managed Disks | Ondersteund.
Versleutelde schijven | Ondersteund.<br/><br/> Er kan een back-up worden Azure Disk Encryption virtuele Azure-Azure Disk Encryption (met of zonder de Azure AD-app).<br/><br/> Versleutelde VM's kunnen niet worden hersteld op bestands-/mapniveau. U moet de hele VM herstellen.<br/><br/> U kunt versleuteling inschakelen op VM's die al door de Azure Backup.
Schijven met Write Accelerator ingeschakeld | Vanaf 23 november 2020 wordt alleen ondersteund in de regio's Korea - centraal (KRC) en Zuid-Afrika - noord (SAN) voor een beperkt aantal abonnementen. Voor deze ondersteunde abonnementen maakt Azure Backup back-up van de virtuele machines met schijven die tijdens de back-up zijn ingeschakeld voor Write Accelerated (WA).<br><br>Voor niet-ondersteunde regio's is een internetverbinding vereist op de VM om momentopnamen te maken van Virtual Machines wa is ingeschakeld.<br><br> **Belangrijke opmerking:** In deze niet-ondersteunde regio's hebben virtuele machines met WA-schijven een internetverbinding nodig voor een geslaagde back-up (ook al zijn deze schijven uitgesloten van de back-up).
Back-up & ontdubbelde VM's/schijven herstellen | Azure Backup biedt geen ondersteuning voor ontdubbeling. Zie dit artikel voor meer [informatie](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  - Azure Backup niet ontdubbelen op alle VM's in de Recovery Services-kluis <br/> <br/>  - Als er VM's zijn met de ontdubbelingstoestand tijdens het herstellen, kunnen de bestanden niet worden hersteld omdat de kluis de indeling niet begrijpt. U kunt echter wel de volledige VM herstellen.
Schijf toevoegen aan beveiligde VM | Ondersteund.
Hetize van schijf op beveiligde VM's | Ondersteund.
Gedeelde opslag| Het maken van back-up van VM'Cluster Shared Volume (CSV) of Scale-Out-bestandsserver wordt niet ondersteund. CSV-schrijvers zullen waarschijnlijk mislukken tijdens het maken van een back-up. Bij het herstellen komen schijven met CSV-volumes mogelijk niet beschikbaar.
[Gedeelde schijven](../virtual-machines/disks-shared-enable.md) | Wordt niet ondersteund.
Ultra - SSD schijven | Wordt niet ondersteund. Zie deze beperkingen voor [meer informatie.](selective-disk-backup-restore.md#limitations)
[Tijdelijke schijven](../virtual-machines/managed-disks-overview.md#temporary-disk) | Er wordt geen back-up van tijdelijke schijven gemaakt door Azure Backup.
NVMe/kortstondige schijven | Wordt niet ondersteund.

## <a name="vm-network-support"></a>VM-netwerkondersteuning

**Onderdeel** | **Ondersteuning**
--- | ---
Aantal netwerkinterfaces (NIC's) | Maximaal aantal NIC's dat wordt ondersteund voor een specifieke Azure-VM-grootte.<br/><br/> NIC's worden gemaakt wanneer de VM wordt gemaakt tijdens het herstelproces.<br/><br/> Het aantal NIC's op de herstelde VM weerspiegelt het aantal NIC's op de VM toen u beveiliging inschakelen. Het verwijderen van NIC's nadat u beveiliging hebt ingeschakeld, heeft geen invloed op het aantal.
Externe/interne load balancer |Ondersteund. <br/><br/> [Meer informatie over](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) het herstellen van VM's met speciale netwerkinstellingen.
Meerdere gereserveerde IP-adressen |Ondersteund. <br/><br/> [Meer informatie over](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) het herstellen van VM's met speciale netwerkinstellingen.
VM's met meerdere netwerkadapters| Ondersteund. <br/><br/> [Meer informatie over](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) het herstellen van VM's met speciale netwerkinstellingen.
VM's met openbare IP-adressen| Ondersteund.<br/><br/> Koppel een bestaand openbaar IP-adres aan de NIC of maak een adres en koppel dit aan de NIC nadat het herstellen is uitgevoerd.
Netwerkbeveiligingsgroep (NSG) op NIC/subnet. |Ondersteund.
Statisch IP-adres | Wordt niet ondersteund.<br/><br/> Aan een nieuwe VM die is gemaakt op een herstelpunt, wordt een dynamisch IP-adres toegewezen.<br/><br/> Voor klassieke VM's kunt u geen back-up maken van een VM met een gereserveerd IP-adres en geen gedefinieerd eindpunt.
Dynamisch IP-adres |Ondersteund.<br/><br/> Als de NIC op de bron-VM dynamische IP-adressering gebruikt, gebruikt de NIC op de herstelde VM deze standaard ook.
Azure Traffic Manager| Ondersteund.<br/><br/>Als de back-up van de VM zich in de Traffic Manager, voegt u de herstelde VM handmatig toe aan hetzelfde Traffic Manager exemplaar.
Azure DNS |Ondersteund.
Aangepaste DNS |Ondersteund.
Uitgaande connectiviteit via HTTP-proxy | Ondersteund.<br/><br/> Een geverifieerde proxy wordt niet ondersteund.
Service-eindpunten voor virtueel netwerk| Ondersteund.<br/><br/> Instellingen voor het opslagaccount voor firewalls en virtuele netwerken moeten toegang vanuit alle netwerken toestaan.

## <a name="vm-security-and-encryption-support"></a>Ondersteuning voor VM-beveiliging en -versleuteling

Azure Backup ondersteunt versleuteling voor in-transit- en inactieve gegevens:

Netwerkverkeer naar Azure:

- Back-upverkeer van servers naar de Recovery Services-kluis wordt versleuteld met behulp Advanced Encryption Standard 256.
- Back-upgegevens worden verzonden via een beveiligde HTTPS-koppeling.
- De back-upgegevens worden versleuteld opgeslagen in de Recovery Services-kluis.
- Alleen u hebt de versleutelingssleutel om deze gegevens te ontgrendelen. De back-upgegevens kunnen niet door Microsoft ontsleuteld.

  > [!WARNING]
  > Nadat u de kluis hebt ingesteld, hebt alleen u toegang tot de versleutelingssleutel. Microsoft bewaart nooit een kopie en heeft geen toegang tot de sleutel. Als de sleutel verkeerd wordt geplaatst, kan Microsoft de back-upgegevens niet herstellen.

Gegevensbeveiliging:

- Wanneer u een back-up van virtuele Azure-machines wilt maken, moet u versleuteling instellen *binnen* de virtuele machine.
- Azure Backup ondersteunt Azure Disk Encryption, die gebruikmaakt van BitLocker op virtuele **Windows-machines en dm-crypt** op virtuele Linux-machines.
- Op de backend maakt Azure Backup gebruik van [Azure Storage Service-versleuteling](../storage/common/storage-service-encryption.md), waarmee data-at-rest worden beschermd.

**Machine** | **In-transit** | **Inactief**
--- | --- | ---
On-premises Windows-machines zonder DPM/MABS | ![Ja][green] | ![Ja][green]
Azure-VM's | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met DPM | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met MABS | ![Ja][green] | ![Ja][green]

## <a name="vm-compression-support"></a>Ondersteuning voor VM-compressie

Back-up ondersteunt de compressie van back-upverkeer, zoals samengevat in de volgende tabel. Houd rekening met het volgende:

- Voor Azure-VM's leest de VM-extensie de gegevens rechtstreeks vanuit het Azure-opslagaccount via het opslagnetwerk. Het is niet nodig om dit verkeer te comprimeren.
- Als u DPM of MABS gebruikt, kunt u bandbreedte besparen door de gegevens te comprimeren voordat er een back-up van wordt gemaakt naar DPM/MABS.

**Machine** | **Comprimeren naar MABS/DPM (TCP)** | **Comprimeren naar kluis (HTTPS)**
--- | --- | ---
On-premises Windows-machines zonder DPM/MABS | N.v.t. | ![Ja][green]
Azure-VM's | NA | NA
On-premises/Azure VM's met DPM | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met MABS | ![Ja][green] | ![Ja][green]

## <a name="next-steps"></a>Volgende stappen

- [Back-up maken van Azure-VM's.](backup-azure-arm-vms-prepare.md)
- [Rechtstreeks een back-up maken van Windows-machines](tutorial-backup-windows-server-to-azure.md), zonder een back-upserver.
- [MABS instellen](backup-azure-microsoft-azure-backup.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar MABS maken.
- [DPM instellen](backup-azure-dpm-introduction.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar DPM maken.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png