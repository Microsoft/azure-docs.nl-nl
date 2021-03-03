---
title: Ondersteuningsmatrix voor back-up van Azure-VM
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen bij het maken van back-ups van virtuele Azure-machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 2536ae0d33767de5ad53740407622e67c582cc37
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710665"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Ondersteuningsmatrix voor back-up van Azure-VM

U kunt de [Azure backup-service](backup-overview.md) gebruiken voor het maken van een back-up van on-premises machines en werk belastingen, en virtuele machines van Azure (vm's). Dit artikel bevat een overzicht van de ondersteunings instellingen en beperkingen bij het maken van back-ups van virtuele Azure-machines met Azure Backup.

Andere ondersteunings matrices:

- [Algemene ondersteunings matrix](backup-support-matrix.md) voor Azure backup
- [Ondersteunings matrix](backup-support-matrix-mabs-dpm.md) voor back-up van Azure Backup Server/System Center-Data Protection Manager (DPM)
- [Ondersteunings matrix](backup-support-matrix-mars-agent.md) voor back-up met de Microsoft Azure Recovery Services-agent (Mars)

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Hier vindt u informatie over het maken van back-ups en het herstellen van virtuele Azure-machines met de Azure Backup-service.

**Scenario** | **Een back-up maken** | **Tussen** |**Herstellen**
--- | --- | --- | ---
Directe back-ups van virtuele Azure-machines  | Maak een back-up van de volledige VM.  | Er is geen extra agent nodig op de Azure-VM. Azure Backup installeert en gebruikt een uitbrei ding van de [Azure VM-agent](../virtual-machines/extensions/agent-windows.md) die wordt uitgevoerd op de VM. | Herstel als volgt:<br/><br/> - **Een basis-VM maken**. Dit is handig als de VM geen speciale configuratie heeft, zoals meerdere IP-adressen.<br/><br/> - **Herstel de VM-schijf**. Herstel de schijf. Koppel deze vervolgens aan een bestaande virtuele machine of maak een nieuwe virtuele machine op basis van de schijf met behulp van Power shell.<br/><br/> - **Vervang de VM-schijf**. Als er een virtuele machine bestaat en beheerde schijven (niet-versleuteld) worden gebruikt, kunt u een schijf herstellen en deze gebruiken om een bestaande schijf op de virtuele machine te vervangen.<br/><br/> - **Specifieke bestanden/mappen herstellen**. U kunt bestanden/mappen herstellen vanaf een virtuele machine in plaats van vanaf de hele VM.
Directe back-ups van virtuele Azure-machines (alleen Windows)  | Maak een back-up van specifieke bestanden/mappen/volume. | Installeer de [Azure Recovery Services-agent](backup-azure-file-folder-backup-faq.md).<br/><br/> U kunt de MARS-agent naast de back-upextensie voor de Azure VM-agent uitvoeren om een back-up te maken van de virtuele machine op het niveau van het bestand of de map. | Specifieke mappen/bestanden herstellen.
Back-up van Azure VM maken op back-upserver  | Back-ups maken van bestanden/mappen/volumes; systeem status/bare metal bestanden; App-gegevens naar System Center DPM of naar Microsoft Azure Backup-Server (MABS).<br/><br/> DPM-MABS maakt vervolgens een back-up van de back-upkluis. | Installeer de DPM/MABS Protection-agent op de VM. De MARS-agent is geïnstalleerd op DPM-MABS.| Bestanden/mappen/volumes herstellen; systeem status/bare metal bestanden; App-gegevens.

Meer informatie over back-ups [met behulp van een back-upserver](backup-architecture.md#architecture-back-up-to-dpmmabs) en over [ondersteunings vereisten](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Ondersteunde back-upacties

**Actie** | **Ondersteuning**
--- | ---
Een back-up maken van een VM die wordt afgesloten/offline VM | Ondersteund.<br/><br/> Moment opname is alleen vastlopen consistent, niet voor app-consistent.
Back-ups maken van schijven na migratie naar Managed disks | Ondersteund.<br/><br/> De back-up blijft werken. Geen actie vereist.
Back-up van beheerde schijven maken na inschakelen van vergren deling van resource groep | Wordt niet ondersteund.<br/><br/> Azure Backup kunt de oudere herstel punten niet verwijderen, waardoor de back-ups mislukken wanneer de maximum limiet van herstel punten is bereikt.
Back-upbeleid voor een VM wijzigen | Ondersteund.<br/><br/> Er wordt een back-up van de VM gemaakt met behulp van de instellingen voor het plannen en bewaren van het nieuwe beleid. Als de Bewaar instellingen worden uitgebreid, worden de bestaande herstel punten gemarkeerd en bewaard. Als ze worden gereduceerd, worden de bestaande herstel punten verwijderd bij de volgende opschoon taak en uiteindelijk gewist.
Een back-uptaak annuleren| Ondersteund tijdens het momentopname proces.<br/><br/> Niet ondersteund wanneer de moment opname wordt overgedragen naar de kluis.
Back-ups maken van de VM naar een andere regio of een ander abonnement |Wordt niet ondersteund.<br><br>Om een back-up te kunnen maken, moeten virtuele machines zich in hetzelfde abonnement als de kluis voor back-up bevallen.
Back-ups per dag (via de Azure VM-extensie) | Eén geplande back-up per dag.<br/><br/>De Azure Backup-service ondersteunt Maxi maal drie back-ups op aanvraag per dag en één extra geplande back-up.
Back-ups per dag (via de MARS-agent) | Drie geplande back-ups per dag.
Back-ups per dag (via DPM/MABS) | Twee geplande back-ups per dag.
Maandelijkse/jaarlijkse back-up| Niet ondersteund bij het maken van een back-up met de Azure VM-extensie. Alleen dagelijks en wekelijks worden ondersteund.<br/><br/> U kunt het beleid zo instellen dat dagelijks/wekelijks back-ups worden bewaard voor een maandelijks/jaarlijks Bewaar periode.
Automatische aanpassing van de klok | Wordt niet ondersteund.<br/><br/> Azure Backup wordt niet automatisch aangepast aan de zomer-en winter tijd wanneer een back-up van een virtuele machine wordt gemaakt.<br/><br/>  Pas het beleid waar nodig handmatig aan.
[Beveiligings functies voor hybride back-ups](./backup-azure-security-feature.md) |Het uitschakelen van beveiligings functies wordt niet ondersteund.
Maak een back-up van de VM waarvan de machine tijd is gewijzigd | Wordt niet ondersteund.<br/><br/> Als de tijd van de machine wordt gewijzigd in een toekomstige datum-tijd na het inschakelen van de back-up voor die VM, wordt een geslaagde back-up echter niet gegarandeerd.

## <a name="operating-system-support-windows"></a>Ondersteuning voor besturings systeem (Windows)

De volgende tabel bevat een overzicht van de ondersteunde besturings systemen bij het maken van een back-up van virtuele Windows Azure-machines.

**Scenario** | **Ondersteuning voor besturings systeem**
--- | ---
Back-up maken met extensie van Azure VM-agent | -Windows 10-client (alleen 64 bits) <br/><br/>-Windows Server 2019 (Data Center/Data Center core/Standard) <br/><br/> -Windows Server 2016 (Data Center/Data Center core/Standard) <br/><br/> -Windows Server 2012 R2 (Data Center/Standard) <br/><br/> -Windows Server 2012 (Data Center/Standard) <br/><br/> -Windows Server 2008 R2 (RTM en SP1 Standard)  <br/><br/> -Windows Server 2008 (alleen 64 bits)
Back-ups maken met MARS-agent | [Ondersteunde](backup-support-matrix-mars-agent.md#supported-operating-systems) besturings systemen.
Back-up maken met DPM-MABS | Ondersteunde besturings systemen voor back-up met [MABS](backup-mabs-protection-matrix.md) en [DPM](/system-center/dpm/dpm-protection-matrix).

Azure Backup biedt geen ondersteuning voor 32-bits besturingssystemen.

## <a name="support-for-linux-backup"></a>Ondersteuning voor Linux-back-up

Dit wordt ondersteund als u een back-up wilt maken van Linux-machines.

**Actie** | **Ondersteuning**
--- | ---
Back-ups van virtuele Linux-machines maken met de Azure VM-agent van Linux | Bestands consistente back-up.<br/><br/> App-consistente back-up met behulp van [aangepaste scripts](backup-azure-linux-app-consistent.md).<br/><br/> Tijdens het herstellen kunt u een nieuwe virtuele machine maken, een schijf herstellen en gebruiken om een virtuele machine te maken of een schijf herstellen en gebruiken om een schijf op een bestaande virtuele machine te vervangen. U kunt ook afzonderlijke bestanden en mappen herstellen.
Back-ups maken van virtuele Linux-machines met een MARS-agent | Wordt niet ondersteund.<br/><br/> De MARS-agent kan alleen worden geïnstalleerd op Windows-machines.
Back-ups maken van virtuele Linux-machines met DPM/MABS | Wordt niet ondersteund.

## <a name="operating-system-support-linux"></a>Ondersteuning voor besturings systeem (Linux)

Voor Azure VM Linux-back-ups ondersteunt Azure Backup de lijst met Linux- [distributies die zijn goedgekeurd door Azure](../virtual-machines/linux/endorsed-distros.md). Houd rekening met het volgende:

- Azure Backup biedt geen ondersteuning voor kern besturingssysteem Linux.
- Azure Backup biedt geen ondersteuning voor 32-bits besturingssystemen.
- Andere uw eigen Linux-distributies kunnen werken zolang de [Azure VM-agent voor Linux](../virtual-machines/extensions/agent-linux.md) beschikbaar is op de virtuele machine en zolang python wordt ondersteund.
- Azure Backup biedt geen ondersteuning voor een door een proxy geconfigureerde Linux-VM als er geen python-versie 2,7 is geïnstalleerd.
- Azure Backup biedt geen ondersteuning voor het maken van back-ups van NFS-bestanden die zijn gekoppeld vanuit opslag, of van andere NFS-servers, op Linux-of Windows-computers. Er worden alleen back-ups gemaakt van schijven die lokaal zijn gekoppeld aan de virtuele machine.

## <a name="backup-frequency-and-retention"></a>Back-upfrequentie en retentie

**Instelling** | **Limieten**
--- | ---
Maximum aantal herstel punten per beveiligd exemplaar (computer/werk belasting) | 9999.
Maximale verloop tijd voor een herstel punt | Geen limiet (99 jaar).
Maximale back-upfrequentie naar kluis (Azure VM-extensie) | Eenmaal per dag.
Maximale back-upfrequentie voor de kluis (MARS-agent) | Drie back-ups per dag.
Maximale back-upfrequentie naar DPM/MABS | Om de 15 minuten voor SQL Server.<br/><br/> Eenmaal per uur voor andere werk belastingen.
Bewaarperiode van herstelpunt | Dagelijks, wekelijks, maandelijks en jaarlijks.
Maximale bewaarperiode | Afhankelijk van back-upfrequentie.
Herstelpunten op DPM-/MABS-schijf | 64 voor bestands servers en 448 voor app-servers.<br/><br/> Tapeherstelpunten zijn onbeperkt voor on-premises DPM.

## <a name="supported-restore-methods"></a>Ondersteunde herstel methoden

**Optie voor herstellen** | **Details**
--- | ---
**Een nieuwe virtuele machine maken** | Hiermee maakt en krijgt u snel een standaard-VM die vanaf een herstelpunt actief is.<br/><br/> U kunt een naam opgeven voor de virtuele machine, de resource groep en het virtuele netwerk (VNet) selecteren waarin deze wordt geplaatst en een opslag account opgeven voor de herstelde VM. De nieuwe VM moet in dezelfde regio worden gemaakt als de bron-VM.
**Schijf herstellen** | Hiermee wordt een VM-schijf hersteld, die vervolgens kan worden gebruikt om een nieuwe virtuele machine te maken.<br/><br/> Azure Backup biedt u een sjabloon waarmee u een nieuwe virtuele machine kunt aanpassen en maken. <br/><br> De herstel taak genereert een sjabloon die u kunt downloaden en gebruiken om aangepaste VM-instellingen op te geven en om een virtuele machine te maken.<br/><br/> De schijven worden gekopieerd naar de resourcegroep die u opgeeft.<br/><br/> U kunt de schijf ook koppelen aan een bestaande virtuele machine of een nieuwe virtuele machine maken met behulp van Power shell.<br/><br/> Deze optie is handig als u de virtuele machine wilt aanpassen, configuratie-instellingen wilt toevoegen die niet waren geconfigureerd op het moment van de back-up of instellingen wilt toevoegen die moeten worden geconfigureerd met de sjabloon of PowerShell.
**Bestaande schijf vervangen** | U kunt een schijf herstellen en gebruiken om een schijf op de bestaande virtuele machine te vervangen.<br/><br/> De huidige VM moet bestaan. Als deze is verwijderd, kan deze optie niet worden gebruikt.<br/><br/> Azure Backup maakt een moment opname van de bestaande virtuele machine voordat de schijf wordt vervangen en slaat deze op in de faserings locatie die u opgeeft. Bestaande schijven die zijn verbonden met de virtuele machine worden vervangen door het geselecteerde herstelpunt.<br/><br/> De moment opname wordt gekopieerd naar de kluis en bewaard in overeenstemming met het Bewaar beleid. <br/><br/> Na de vervangen schijf bewerking wordt de oorspronkelijke schijf in de resource groep bewaard. U kunt ervoor kiezen om de oorspronkelijke schijven hand matig te verwijderen als ze niet nodig zijn. <br/><br/>Bestaande vervangen wordt ondersteund voor niet-versleutelde beheerde Vm's en voor virtuele machines [die zijn gemaakt met aangepaste installatie kopieën](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Het wordt niet ondersteund voor niet-beheerde schijven en Vm's, klassieke Vm's en [gegeneraliseerde vm's](../virtual-machines/windows/capture-image-resource.md).<br/><br/> Als het herstel punt meer of minder schijven heeft dan de huidige virtuele machine, wordt in het aantal schijven in het herstel punt alleen de VM-configuratie weer gegeven.<br><br> Bestaande vervangen wordt ook ondersteund voor Vm's met gekoppelde resources, zoals door de [gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) en [Key Vault](../key-vault/general/overview.md).
**In meerdere regio's (secundaire regio)** | Het terugzetten van meerdere regio's kan worden gebruikt om virtuele Azure-machines in de secundaire regio te herstellen. Dit is een [Azure-gekoppelde regio](../best-practices-availability-paired-regions.md#what-are-paired-regions).<br><br> U kunt alle virtuele machines van Azure voor het geselecteerde herstel punt herstellen als de back-up wordt uitgevoerd in de secundaire regio.<br><br> Deze functie is beschikbaar voor de volgende opties:<br> <li> [Een VM maken](./backup-azure-arm-restore-vms.md#create-a-vm): <br> <li> [Schijven herstellen](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> De optie [bestaande schijven vervangen](./backup-azure-arm-restore-vms.md#replace-existing-disks) wordt momenteel niet ondersteund.<br><br> Machtigingen<br> De herstel bewerking op de secundaire regio kan worden uitgevoerd met back-upbeheerders en app-beheerders.

## <a name="support-for-file-level-restore"></a>Ondersteuning voor herstel op bestands niveau

**Herstellen** | **Ondersteund**
--- | ---
Bestanden herstellen over besturings systemen | U kunt bestanden herstellen op elke computer die hetzelfde (of compatibel) besturings systeem heeft als de back-up van de virtuele machine. Zie de [tabel met compatibele besturings systemen](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).
Bestanden herstellen van versleutelde Vm's | Wordt niet ondersteund.
Bestanden herstellen van opslag accounts met netwerk beperkingen | Wordt niet ondersteund.
Bestanden op Vm's terugzetten met behulp van Windows-opslag ruimten | Terugzetten wordt niet ondersteund op dezelfde VM.<br/><br/> In plaats daarvan herstelt u de bestanden op een compatibele VM.
Bestanden herstellen op een Linux-VM met behulp van LVM/RAID-matrices | Terugzetten wordt niet ondersteund op dezelfde VM.<br/><br/> Herstel op een compatibele VM.
Bestanden herstellen met speciale netwerk instellingen | Terugzetten wordt niet ondersteund op dezelfde VM. <br/><br/> Herstel op een compatibele VM.
Bestanden herstellen vanaf een gedeelde schijf, een tijdelijk station, een ontdubbelde schijf, Ultra disk en schijf waarop schrijf versnelling is ingeschakeld | Herstel wordt niet ondersteund, <br/><br/>Zie [ondersteuning voor Azure VM-opslag](#vm-storage-support).

## <a name="support-for-vm-management"></a>Ondersteuning voor VM-beheer

De volgende tabel bevat een overzicht van de ondersteuning voor back-ups tijdens taken voor VM-beheer, zoals het toevoegen of vervangen van VM-schijven.

**Herstellen** | **Ondersteund**
--- | ---
Herstellen in het abonnement/de regio/zone. | Wordt niet ondersteund.
Herstellen naar een bestaande virtuele machine | Gebruik de optie schijf vervangen.
Schijf herstellen met een opslag account dat is ingeschakeld voor Azure Storage-service versleuteling (SSE) | Wordt niet ondersteund.<br/><br/> Herstel naar een account waarvoor geen SSE is ingeschakeld.
Herstellen naar gemengde opslag accounts |Wordt niet ondersteund.<br/><br/> Op basis van het type opslag account zijn alle herstelde schijven Premium of Standard en niet gemengd.
Herstel de VM rechtstreeks in een beschikbaarheidsset | Voor Managed disks kunt u de schijf herstellen en de optie beschikbaarheidsset gebruiken in de sjabloon.<br/><br/> Niet ondersteund voor niet-beheerde schijven. Voor niet-beheerde schijven herstelt u de schijf en maakt u vervolgens een virtuele machine in de beschikbaarheidsset.
Back-ups van niet-beheerde Vm's herstellen na een upgrade naar een beheerde VM| Ondersteund.<br/><br/> U kunt schijven herstellen en vervolgens een beheerde virtuele machine maken.
Herstel de VM naar een herstel punt voordat de virtuele machine is gemigreerd naar Managed disks | Ondersteund.<br/><br/> U kunt herstellen naar onbeheerde schijven (standaard), de herstelde schijven converteren naar beheerde schijven en een virtuele machine maken met de beheerde schijven.
Een VM herstellen die is verwijderd. | Ondersteund.<br/><br/> U kunt de virtuele machine herstellen vanaf een herstel punt.
Een VM van een domein controller herstellen  | Ondersteund. Zie [vm's van domein controllers herstellen](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)voor meer informatie.
VM herstellen in een ander virtueel netwerk |Ondersteund.<br/><br/> Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden.

## <a name="vm-compute-support"></a>Ondersteuning voor VM-berekening

**Compute** | **Ondersteuning**
--- | ---
VM-grootte |Een Azure VM-grootte met ten minste twee CPU-kernen en 1 GB RAM-geheugen.<br/><br/> [Meer informatie.](../virtual-machines/sizes.md)
Back-ups maken van Vm's in [beschikbaarheids sets](../virtual-machines/availability.md#availability-sets) | Ondersteund.<br/><br/> U kunt een virtuele machine niet herstellen in een beschik bare set met behulp van de optie om snel een virtuele machine te maken. In plaats daarvan moet u, wanneer u de virtuele machine herstelt, de schijf herstellen en gebruiken om een virtuele machine te implementeren, of een schijf herstellen en gebruiken om een bestaande schijf te vervangen.
Back-ups maken van Vm's die zijn geïmplementeerd met het [voor deel voor hybride gebruik (hub)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Ondersteund.
Back-ups maken van Vm's die zijn geïmplementeerd vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Gepubliceerd door micro soft, derden) |Ondersteund.<br/><br/> Op de VM moet een ondersteund besturings systeem worden uitgevoerd.<br/><br/> Bij het herstellen van bestanden op de VM kunt u alleen herstellen naar een compatibel besturings systeem (geen besturings systeem dat ouder of later is). Azure Marketplace-Vm's die worden ondersteund als Vm's, worden niet hersteld, omdat deze behoefte hebben aan inkoop gegevens. Ze worden alleen teruggezet als schijven.
Back-ups maken van Vm's die zijn geïmplementeerd vanuit een aangepaste installatie kopie (derden) |Ondersteund.<br/><br/> Op de VM moet een ondersteund besturings systeem worden uitgevoerd.<br/><br/> Bij het herstellen van bestanden op de VM kunt u alleen herstellen naar een compatibel besturings systeem (geen besturings systeem dat ouder of later is).
Back-ups maken van virtuele machines die zijn gemigreerd naar Azure| Ondersteund.<br/><br/> Als u een back-up wilt maken van de VM, moet de VM-agent op de gemigreerde computer zijn geïnstalleerd.
Back-up van multi-VM-consistentie | Azure Backup biedt geen consistentie van gegevens en toepassingen op meerdere Vm's.
Back-up met [Diagnostische instellingen](../azure-monitor/essentials/platform-logs-overview.md)  | Niet-ondersteunde. <br/><br/> Als het herstellen van de virtuele machine van Azure met Diagnostische instellingen wordt geactiveerd met de optie [nieuwe maken](backup-azure-arm-restore-vms.md#create-a-vm) , mislukt de herstel bewerking.
Herstellen van op zones vastgemaakte Vm's | Ondersteund (voor een VM waarvan een back-up is gemaakt na 2019 januari en waar [beschikbaarheids zones](https://azure.microsoft.com/global-infrastructure/availability-zones/) beschikbaar zijn).<br/><br/>We ondersteunen momenteel het herstellen naar dezelfde zone die is vastgemaakt in Vm's. Als de zone echter niet beschikbaar is vanwege een storing, mislukt de herstel bewerking.
Gen2 Vm's | Ondersteund <br> Azure Backup ondersteunt het maken van back-ups en het herstellen van [Gen2-vm's](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Wanneer deze Vm's worden hersteld vanaf het herstel punt, worden ze teruggezet als [Gen2 vm's](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).
Back-ups van virtuele Azure-machines met vergren delingen | Niet ondersteund voor niet-beheerde Vm's. <br><br> Ondersteund voor beheerde Vm's.
[Spot-VM's](../virtual-machines/spot-vms.md) | Niet-ondersteunde. Azure Backup de locatie van de virtuele machines als gewone virtuele machines van Azure herstelt.
[Voor Azure toegewezen host](../virtual-machines/dedicated-hosts.md) | Ondersteund
Windows-opslag ruimten configureren van zelfstandige virtuele machines in azure | Ondersteund

## <a name="vm-storage-support"></a>Ondersteuning voor VM-opslag

**Onderdeel** | **Ondersteuning**
--- | ---
Azure VM-gegevensschijven | Ondersteuning voor back-ups van virtuele Azure-machines met Maxi maal 32 schijven.<br><br> Ondersteuning voor het maken van back-ups van virtuele Azure-machines met onbeheerde schijven of klassieke Vm's is Maxi maal 16 schijven.
Grootte van de gegevens schijf | De afzonderlijke schijf grootte kan Maxi maal 32 TB en Maxi maal 256 TB gecombineerd voor alle schijven in een VM zijn.
Opslagtype | Standard-HDD, Standard-SSD Premium-SSD.
Managed Disks | Ondersteund.
Versleutelde schijven | Ondersteund.<br/><br/> Voor Azure-Vm's met Azure Disk Encryption kan een back-up worden gemaakt (met of zonder de Azure AD-app).<br/><br/> Versleutelde Vm's kunnen niet worden hersteld op het niveau van het bestand of de map. U moet de volledige VM herstellen.<br/><br/> U kunt versleuteling inschakelen voor virtuele machines die al worden beveiligd door Azure Backup.
Schijven waarop Write Accelerator is ingeschakeld | Vanaf 23 november 2020 wordt alleen ondersteund in de regio's Korea-centraal (KRC) en Zuid-Afrika-noord (SAN) voor een beperkt aantal abonnementen. Voor die ondersteunde abonnementen maakt Azure Backup een back-up van de virtuele machines met schijven die zijn geschreven tijdens het maken van een back-up.<br><br>Voor de niet-ondersteunde regio's is de Internet verbinding op de VM vereist voor het maken van moment opnamen van Virtual Machines waarbij WA is ingeschakeld.<br><br> **Belang rijke Opmerking**: in deze niet-ondersteunde regio's hebben virtuele machines met WA-schijven Internet connectiviteit nodig voor een geslaagde back-up (zelfs als deze schijven worden uitgesloten van de back-up).
Back-up maken & ontdubbelde Vm's/schijven herstellen | Azure Backup biedt geen ondersteuning voor ontdubbeling. Raadpleeg dit [artikel](./backup-support-matrix.md#disk-deduplication-support) voor meer informatie <br/> <br/>  -Azure Backup wordt niet gedupliceerd over Vm's in de Recovery Services kluis <br/> <br/>  -Als er Vm's aanwezig zijn tijdens het herstellen, kunnen de bestanden niet worden hersteld omdat de kluis de indeling niet begrijpt. U kunt echter de volledige VM-herstel bewerking uitvoeren.
Schijf toevoegen aan beveiligde VM | Ondersteund.
Grootte van schijf op beveiligde virtuele machine wijzigen | Ondersteund.
Gedeelde opslag| Het maken van back-ups van virtuele machines met Cluster Shared Volume (CSV) of Scale-Out Bestands server wordt niet ondersteund. CSV-schrijvers mislukken waarschijnlijk tijdens het maken van de back-up. Bij het terugzetten kunnen schijven met CSV-volumes mogelijk niet worden opgehaald.
[Gedeelde schijven](../virtual-machines/disks-shared-enable.md) | Wordt niet ondersteund.
Ultra-SSD schijven | Wordt niet ondersteund. Zie deze [beperkingen](selective-disk-backup-restore.md#limitations)voor meer informatie.
[Tijdelijke schijven](../virtual-machines/managed-disks-overview.md#temporary-disk) | Er wordt geen back-up van de tijdelijke schijven gemaakt door Azure Backup.

## <a name="vm-network-support"></a>VM-netwerk ondersteuning

**Onderdeel** | **Ondersteuning**
--- | ---
Aantal netwerk interfaces (Nic's) | Maxi maal maximum aantal Nic's dat wordt ondersteund voor een specifieke Azure VM-grootte.<br/><br/> Nic's worden gemaakt wanneer de virtuele machine tijdens het herstel proces wordt gemaakt.<br/><br/> Het aantal Nic's op de herstelde VM komt overeen met het aantal Nic's op de virtuele machine wanneer u beveiliging inschakelt. Wanneer u de beveiliging inschakelt, heeft dit geen invloed op het aantal.
Externe/interne load balancer |Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van vm's met speciale netwerk instellingen.
Meerdere gereserveerde IP-adressen |Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van vm's met speciale netwerk instellingen.
Vm's met meerdere netwerk adapters| Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van vm's met speciale netwerk instellingen.
Vm's met open bare IP-adressen| Ondersteund.<br/><br/> Een bestaand openbaar IP-adres koppelen aan de NIC, of een adres maken en dit koppelen aan de NIC nadat het terugzetten is voltooid.
Netwerk beveiligings groep (NSG) op NIC/subnet. |Ondersteund.
Statisch IP-adres | Wordt niet ondersteund.<br/><br/> Er wordt een dynamisch IP-adres toegewezen aan een nieuwe virtuele machine die is gemaakt op basis van een herstel punt.<br/><br/> Voor klassieke Vm's kunt u geen back-up maken van een virtuele machine met een gereserveerd IP-adres en geen gedefinieerd eind punt.
Dynamisch IP-adres |Ondersteund.<br/><br/> Als de NIC op de bron-VM gebruikmaakt van dynamische IP-adres Sering, wordt deze ook gebruikt door de NIC op de herstelde VM.
Azure Traffic Manager| Ondersteund.<br/><br/>Als de back-up van de virtuele machine is Traffic Manager, voegt u de herstelde VM hand matig toe aan hetzelfde Traffic Manager exemplaar.
Azure DNS |Ondersteund.
Aangepaste DNS |Ondersteund.
Uitgaande connectiviteit via HTTP-proxy | Ondersteund.<br/><br/> Een geverifieerde proxy wordt niet ondersteund.
Service-eindpunten voor virtueel netwerk| Ondersteund.<br/><br/> De instellingen voor het opslag account van de firewall en het virtuele netwerk moeten toegang toestaan vanuit alle netwerken.

## <a name="vm-security-and-encryption-support"></a>Ondersteuning voor VM-beveiliging en-versleuteling

Azure Backup ondersteunt versleuteling voor in-transit- en inactieve gegevens:

Netwerkverkeer naar Azure:

- Back-upverkeer van servers naar de Recovery Services-kluis wordt versleuteld met behulp van Advanced Encryption Standard 256.
- Back-upgegevens worden verzonden via een beveiligde HTTPS-koppeling.
- De back-upgegevens worden versleuteld opgeslagen in de Recovery Services-kluis.
- Alleen u hebt de versleutelings sleutel om deze gegevens te ontgrendelen. De back-upgegevens kunnen niet door Microsoft ontsleuteld.

  > [!WARNING]
  > Nadat u de kluis hebt ingesteld, hebt u alleen toegang tot de versleutelings sleutel. Microsoft bewaart nooit een kopie en heeft geen toegang tot de sleutel. Als de sleutel verkeerd wordt geplaatst, kan Microsoft de back-upgegevens niet herstellen.

Gegevensbeveiliging:

- Bij het maken van een back-up van virtuele Azure-machines moet u versleuteling instellen *binnen* de VM.
- Azure Backup ondersteunt Azure Disk Encryption, dat gebruikmaakt van BitLocker op virtuele Windows-machines en US **DM-cryptografie** op virtuele Linux-machines.
- Op de backend maakt Azure Backup gebruik van [Azure Storage Service-versleuteling](../storage/common/storage-service-encryption.md), waarmee data-at-rest worden beschermd.

**Machine** | **In-transit** | **Inactief**
--- | --- | ---
On-premises Windows-machines zonder DPM/MABS | ![Ja][green] | ![Ja][green]
Azure-VM's | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met DPM | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met MABS | ![Ja][green] | ![Ja][green]

## <a name="vm-compression-support"></a>Ondersteuning voor VM-compressie

Backup ondersteunt de compressie van het back-upverkeer, zoals wordt beschreven in de volgende tabel. Houd rekening met het volgende:

- Voor virtuele Azure-machines leest de VM-extensie de gegevens rechtstreeks vanuit het Azure Storage-account via het opslag netwerk. Het is niet nodig om dit verkeer te comprimeren.
- Als u DPM of MABS gebruikt, kunt u band breedte besparen door de gegevens te comprimeren voordat er een back-up wordt gemaakt naar DPM/MABS.

**Machine** | **Comprimeren naar MABS/DPM (TCP)** | **Comprimeren naar kluis (HTTPS)**
--- | --- | ---
On-premises Windows-machines zonder DPM/MABS | N.v.t. | ![Ja][green]
Azure-VM's | NA | NA
On-premises/Azure VM's met DPM | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met MABS | ![Ja][green] | ![Ja][green]

## <a name="next-steps"></a>Volgende stappen

- [Maak een back-up van virtuele Azure-machines](backup-azure-arm-vms-prepare.md).
- [Rechtstreeks een back-up maken van Windows-machines](tutorial-backup-windows-server-to-azure.md), zonder een back-upserver.
- [MABS instellen](backup-azure-microsoft-azure-backup.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar MABS maken.
- [DPM instellen](backup-azure-dpm-introduction.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar DPM maken.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png