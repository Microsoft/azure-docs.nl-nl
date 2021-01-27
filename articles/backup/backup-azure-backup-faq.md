---
title: Antwoorden op veelgestelde vragen
description: 'Antwoorden op veelgestelde vragen over de functies van Azure Backup, waaronder de Recovery Services-kluis, waarvan een back-up kan worden gemaakt, hoe het werkt, versleuteling en limieten. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: f819440001180a3c446f366e61e3ac0f983fa67f
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806649"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup - veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Backup-service.

## <a name="recovery-services-vault"></a>Recovery Services-kluis

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Is er een limiet voor het aantal kluizen dat voor elk Azure-abonnement kan worden gemaakt?

Ja. U kunt per abonnement maximaal 500 Recovery Services-kluizen maken, per ondersteunde regio van Azure Backup. Als u extra kluizen nodig hebt, maakt u een extra abonnement.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Zijn er beperkingen met betrekking tot het aantal servers/machines dat kan worden geregistreerd voor elke kluis?

U kunt per kluis maximaal 1000 virtuele Azure-machines registreren. Als u de Microsoft Azure Backup-agent gebruikt, kunt u Maxi maal 50 MARS-agents per kluis registreren. En u kunt 50 MABS-servers/DPM-servers registreren bij een kluis.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Hoeveel gegevensbronnen/items kunnen worden beveiligd in een kluis?

U kunt Maxi maal 2000 gegevens bronnen of-items beveiligen voor alle werk belastingen (zoals IaaS VM, SQL, AFS) in een kluis.
Als u bijvoorbeeld al 500 Vm's en 400 Azure Files shares in de kluis hebt beveiligd, kunt u Maxi maal 1100 SQL-data bases in het bestand beveiligen.

### <a name="how-many-policies-can-i-create-per-vault"></a>Hoeveel beleidsregels kan ik maken per kluis?

U kunt maximaal 200 beleidsregels per kluis hebben.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Hoe kan ik, bij het herstellen van gegevens, de gegevens van verschillende servers isoleren, als mijn organisatie één kluis heeft?

Servergegevens die u samen wilt herstellen, moeten dezelfde wachtwoordzin gebruiken bij het instellen een back-up. Als u herstel wilt isoleren op een specifieke server of servers, gebruikt u een wachtwoordzin die alleen bestemd is voor deze server of servers. U kunt bijvoorbeeld verschillende wachtwoordzinnen voor de human resource-server, de accountingserver en de opslagserver gebruiken.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Kan ik mijn kluis verplaatsen van het ene naar het andere abonnement?

Ja. Raadpleeg dit [artikel](backup-azure-move-recovery-services-vault.md) als u een Recovery Services-kluis wilt verplaatsen

### <a name="can-i-move-backup-data-to-another-vault"></a>Kan ik back-upgegevens verplaatsen naar een andere kluis?

Nee. Back-upgegevens die zijn opgeslagen in een kluis, kunnen niet worden verplaatst naar een andere kluis.

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>Kan ik de instelling voor opslag redundantie wijzigen na een back-up?

Het type opslag replicatie is standaard ingesteld op geografisch redundante opslag (GRS). Zodra u de back-up hebt geconfigureerd, is de optie om te wijzigen uitgeschakeld en kan deze niet meer worden gewijzigd.

![Type opslagreplicatie](./media/backup-azure-backup-faq/storage-replication-type.png)

Als u de back-up al hebt geconfigureerd en wilt overstappen van GRS naar LRS, raadpleegt u [hoe u kunt wijzigen van GRS in LRS na het configureren van de back-up](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup).

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Kan ik herstellen op itemniveau (ILR) uitvoeren voor VM's waarvan een back-up is gemaakt in een Recovery Services-kluis?

- ILR wordt ondersteund voor Azure-VM's waarvan een back-up is gemaakt met Azure VM Backup. Raadpleeg dit [artikel](backup-azure-restore-files-from-vm.md) voor meer informatie
- ILR wordt niet ondersteund voor online herstel punten van on-premises Vm's waarvan een back-up is gemaakt door Azure Backup Server (MABS) of System Center DPM.

### <a name="how-can-i-move-data-from-the-recovery-services-vault-to-on-premises"></a>Hoe kan ik gegevens uit de Recovery Services-kluis verplaatsen naar on-premises?

Het is niet mogelijk om gegevens rechtstreeks vanuit de Recovery Services kluis naar on-premises te exporteren met behulp van Data Box. Gegevens moeten worden hersteld naar een opslag account en vervolgens kunnen worden verplaatst naar on-premises via [Data Box](../databox/data-box-overview.md) of [importeren/exporteren](../import-export/storage-import-export-service.md).

### <a name="what-is-the-difference-between-a-geo-redundant-storage-grs-vault-with-and-without-the-cross-region-restore-crr-capability-enabled"></a>Wat is het verschil tussen een GRS-kluis (geo-redundante opslag) met en zonder dat de functie voor CRR (cross-Region Restore) is ingeschakeld?

In het geval van een [GRS](azure-backup-glossary.md#grs) -kluis zonder [CRR](azure-backup-glossary.md#cross-region-restore-crr) -functionaliteit is ingeschakeld, zijn de gegevens in de secundaire regio pas toegankelijk als Azure een nood geval in de primaire regio declareert. In een dergelijk scenario gebeurt het herstel van de secundaire regio. Als CRR is ingeschakeld, zelfs als de primaire regio actief is, kunt u een herstel bewerking activeren in de secundaire regio.

## <a name="azure-backup-agent"></a>Azure Backup-agent

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Waar vind ik veelgestelde vragen over de Azure Backup-agent voor Azure VM Backup?

- Lees deze [Veelgestelde vragen](backup-azure-vm-backup-faq.yml) voor informatie over de agent die wordt uitgevoerd op Azure-VM’s.
- Lees deze [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md) voor informatie over de agent die wordt gebruikt voor het maken van back-ups van Azure-bestandsmappen.

## <a name="general-backup"></a>Algemene back-up

### <a name="are-there-limits-on-backup-scheduling"></a>Gelden er limieten voor de back-upplanning?

Ja.

- U kunt maximaal drie keer per dag een back-up maken van Windows Server- of Windows-machines. U kunt het planningsbeleid instellen op dagelijkse of wekelijkse planningen.
- U kunt maximaal twee keer per dag een back-up van DPM maken. U kunt het planningsbeleid instellen op dagelijks, wekelijks, maandelijks en jaarlijks.
- U maakt eenmaal per dag een back-up van Azure-VM's.

### <a name="what-operating-systems-are-supported-for-backup"></a>Welke besturingssystemen worden ondersteund voor Backup?

Azure Backup biedt ondersteuning voor deze besturingssystemen voor het maken van back-ups van bestanden en mappen, en apps die worden beveiligd met Azure Backup Server en SCDPM.

**Besturingssysteem** | **SKU** | **Details**
--- | --- | ---
Werkstation | |
Windows 10 64-bits | Enterprise, Pro, Home | Op machines moeten de nieuwste servicepacks en updates worden uitgevoerd.
Windows 8.1 64-bits | Enterprise, Pro | Op machines moeten de nieuwste servicepacks en updates worden uitgevoerd.
Windows 8 64-bits | Enterprise, Pro | Op machines moeten de nieuwste servicepacks en updates worden uitgevoerd.
Windows 7 64-bits | Ultimate, Enterprise, Professional, Home Premium Home Basic, Starter | Op machines moeten de nieuwste servicepacks en updates worden uitgevoerd.
Server | |
Windows Server 2019 64-bits | Standard, Datacenter, Essentials | Met de nieuwste servicepacks/updates.
Windows Server 2016 64-bits | Standard, Datacenter, Essentials | Met de nieuwste servicepacks/updates.
Windows Server 2012 R2 64-bits | Standard, Datacenter, Foundation | Met de nieuwste servicepacks/updates.
Windows Server 2012 64-bits | Datacenter, Foundation, Standard | Met de nieuwste servicepacks/updates.
Windows Storage Server 2016 64-bits | Standard, Workgroup | Met de nieuwste servicepacks/updates.
Windows Storage Server 2012 R2 64-bits | Standard, Workgroup, Essential | Met de nieuwste servicepacks/updates.
Windows Storage Server 2012 64-bits | Standard, Workgroup | Met de nieuwste servicepacks/updates.
Windows Server 2008 R2 SP1 64-bits | Standard, Enterprise, Datacenter, Foundation | Installeer de nieuwste updates.
Windows Server 2008 64-bits | Standard, Enterprise, Datacenter | Met de nieuwste updates.

Azure Backup biedt geen ondersteuning voor 32-bits besturingssystemen.

Voor Azure VM Linux-back-ups biedt Azure Backup ondersteuning voor [de lijst met distributies die zijn goedgekeurd in Azure](../virtual-machines/linux/endorsed-distros.md), met uitzondering van Core OS Linux en een 32-bits besturingssysteem. Andere Bring-Your-Own-Linux-distributies werken mogelijk ook, mits de VM-agent beschikbaar is op de VM, en ondersteuning voor Python aanwezig is.

### <a name="are-there-size-limits-for-data-backup"></a>Zijn er groottelimieten voor gegevensback-ups?

De groottelimieten zijn:

OS/machine | Groottelimiet van gegevensbron
--- | ---
Windows 8 of hoger | 54.400 GB
Windows 7 |1700 GB
Windows Server 2012 of hoger | 54.400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | Zie de [ondersteunings matrix voor Azure VM backup](./backup-support-matrix-iaas.md#vm-storage-support)

### <a name="how-is-the-data-source-size-determined"></a>Hoe wordt de grootte van de gegevensbron bepaald?

In de volgende tabel wordt uitgelegd hoe de grootte voor elke gegevensbron wordt bepaald.

**Gegevensbron** | **Details**
--- | ---
Volume |De hoeveelheid gegevens van één volume-VM waarvan een back-up wordt gemaakt.
SQL Server-database |Grootte van de grootte van één data base waarvan een back-up wordt gemaakt.
SharePoint | Som van de inhoud en configuratiedatabases in een SharePoint-farm waarvan een back-up wordt gemaakt.
Exchange |Som van alle Exchange-databases op een Exchange-server waarvan een back-up wordt gemaakt.
BMR/systeemstatus |Elke afzonderlijke kopie van de BMR of systeemstatus van de machine waarvan een back-up wordt gemaakt.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Is er een limiet voor de hoeveelheid gegevens waarvan een back-up wordt gemaakt met een Recovery Services-kluis?

Er is geen limiet voor de totale hoeveelheid gegevens waarvan u een back-up kunt maken met behulp van een Recovery Services kluis. De afzonderlijke gegevens bronnen (met uitzonde ring van virtuele machines van Azure) kunnen Maxi maal 54.400 GB groot zijn. Zie de [sectie kluis limieten in de ondersteunings matrix](./backup-support-matrix.md#vault-support)voor meer informatie over limieten.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Waarom is de omvang van de gegevens die worden overgebracht naar de Recovery Services-kluis, kleiner dan de hoeveelheid gegevens die zijn geselecteerd voor een back-up?

Gegevens waarvan een back-up wordt gemaakt via Azure Backup-agent, DPM en Azure Backup Server, worden gecomprimeerd en versleuteld voordat ze worden overgedragen. Wanneer de gegevens zijn gecomprimeerd en versleuteld, zijn de gegevens in de kluis 30 tot 40% kleiner.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Kan ik afzonderlijke bestanden verwijderen vanaf een herstelpunt in de kluis?

Nee, Azure Backup biedt geen ondersteuning voor het verwijderen of opschonen van afzonderlijke items uit opgeslagen back-ups.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Als ik een back-uptaak annuleer nadat deze is gestart, worden de overgedragen back-upgegevens dan verwijderd?

Nee. Alle gegevens die zijn overgebracht naar de kluis voordat de back-uptaak wordt geannuleerd, blijven aanwezig in de kluis.

- Azure Backup gebruikt een controlepuntmechanisme waarmee tijdens de back-up van tijd tot tijd controlepunten worden toegevoegd aan de back-upgegevens.
- Omdat de back-upgegevens controlepunten bevatten, kan tijdens het volgende back-upproces de integriteit van de bestanden worden gecontroleerd.
- De volgende back-uptaak is incrementeel ten opzichte van de gegevens waarvan eerder een back-up is gemaakt. Incrementele back-ups dragen alleen nieuwe of gewijzigde gegevens over, zodat de bandbreedte beter wordt benut.

Als u een back-uptaak voor een virtuele Azure-machine annuleert, worden eventuele overgedragen gegevens geannuleerd. De volgende back-uptaak draagt incrementele gegevens over van na de vorige succesvolle back-up-taak.

## <a name="retention-and-recovery"></a>Retentie en herstel

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Zijn het bewaarbeleid voor DPM-machines en voor Windows-computers zonder DPM gelijk?

Ja, ze hebben beiden dagelijks, wekelijks, maandelijks en jaarlijks bewaarbeleid.

### <a name="can-i-customize-retention-policies"></a>Kan ik het bewaarbeleid aanpassen?

Ja, u kunt beleidsregels aanpassen. U kunt bijvoorbeeld wekelijkse en dagelijkse retentievereisten configureren, maar niet jaarlijkse en maandelijkse.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Kan ik verschillende tijdstippen gebruiken voor de back-upplanning en het bewaarbeleid?

Nee. Het bewaarbeleid kan alleen worden toegepast op back-uppunten. In deze afbeelding ziet u bijvoorbeeld bewaarbeleid voor back-ups die zijn gemaakt om 12:00 uur en 18:00 uur.

![Back-up en retentie plannen](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Als een back-up langdurig wordt bewaard, duurt het dan langer om een oud gegevenspunt te herstellen?

Nee. De tijd die nodig is om het oudste of nieuwste punt te herstellen, is hetzelfde. Elk herstelpunt gedraagt zich als een volledig punt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Als elk herstelpunt een volledig punt is, heeft dit dan invloed op de totale factureerbare back-upopslag?

Producten met een lange bewaartermijn slaan de back-upgegevens doorgaans op als volledige punten.

- De volledige punten maken *inefficiënt* gebruik van de opslagruimte, maar kunnen wel gemakkelijker en sneller worden hersteld.
- Incrementele kopieën maken *efficiënt* gebruik van de opslagruimte, maar vereisen dat u een keten van gegevens hersteld, wat van invloed is op de hersteltijd

De opslagarchitectuur van Azure Backup biedt u het beste van beide werelden door de gegevens optimaal op te slaan voor snelle herstelbewerkingen en lage opslagkosten. Dit zorgt ervoor dat uw bandbreedte voor inkomend en uitgaand verkeer efficiënt wordt gebruikt. De hoeveelheid gegevens die moet worden opgeslagen, en de benodigde tijd om de gegevens te herstellen, blijft tot een minimum beperkt. Meer informatie over [incrementele back-ups](backup-architecture.md#backup-types).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Geldt er een limiet voor het aantal herstelpunten dat kan worden gemaakt?

U kunt maximaal 9999 herstelpunten maken per beveiligd exemplaar. Een beveiligd exemplaar is een computer, een server (fysiek of virtueel) of een workload waarvan een back-up wordt gemaakt in Azure.

- Meer informatie over [back-up en retentie](./backup-support-matrix.md).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Hoe vaak kan ik gegevens herstellen waarvan een back-up is gemaakt in Azure?

Er is geen limiet voor het aantal herstel bewerkingen van Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Als ik gegevens ga herstellen, betaal ik dan voor het uitgaande verkeer van Azure?

Nee. Herstellen is gratis en er worden geen kosten in rekening gebracht voor het uitgaande verkeer.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Wat gebeurt er wanneer ik het back-upbeleid wijzig?

Wanneer een nieuw beleid wordt toegepast, worden het schema en de retentie van het nieuwe beleid gevolgd.

- Als de retentie is uitgebreid, worden de bestaande herstel punten gemarkeerd om ze te bewaren volgens het nieuwe beleid.
- Als de retentie is beperkt, worden de bestaande herstelpunten gemarkeerd voor verwijdering in de eerstvolgende opschoontaak, en worden ze vervolgens verwijderd.

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>Hoe lang blijven gegevens behouden bij het stoppen van back-ups, maar selecteert u de optie voor het bewaren van back-upgegevens?

Wanneer back-ups worden gestopt en de gegevens worden bewaard, worden de bestaande beleids regels voor het weghalen van gegevens niet meer ondersteund en blijven de gegevens oneindig behouden totdat de beheerder de verwijdering heeft gestart.

## <a name="encryption"></a>Versleuteling

### <a name="is-the-data-sent-to-azure-encrypted"></a>Worden de gegevens die naar Azure worden verzonden, versleuteld?

Ja. Gegevens worden versleuteld op de on-premises machine met behulp van AES256. De gegevens worden verzonden via een beveiligde HTTPS-koppeling. De gegevens die worden verzonden in de cloud, zijn alleen met een HTTPS-koppeling beveiligd tussen de opslag en herstelservice. Met het iSCSI-protocol zijn de gegevens beveiligd die worden verzonden tussen de herstelservice en de computer van de gebruiker. Beveiligde tunneling wordt gebruikt om het iSCSI-kanaal te beveiligen.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Worden de back-upgegevens op Azure ook versleuteld?

Ja. De gegevens in Azure zijn in rust versleuteld.

- Voor on-premises back-ups wordt versleuteling in rust geboden met de wachtwoordzin die u opgeeft wanneer u een back-up maakt in Azure.
- Voot Azure-VM’s worden gegevens in rust versleuteld met behulp van SSE (Storage Service Encryption).

Micro soft ontsleutelt de back-upgegevens op geen enkel moment.

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>Wat is de minimale lengte van de versleutelings sleutel die wordt gebruikt om back-upgegevens te versleutelen?

De versleutelings sleutel die wordt gebruikt door de Microsoft Azure Recovery Services-agent (MARS) is afgeleid van een wachtwoordzin die ten minste 16 tekens lang moet zijn. Voor virtuele Azure-machines geldt geen limiet voor de lengte van de sleutels die worden gebruikt door Azure-sleutel kluis.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Wat gebeurt er als ik de versleutelingssleutel kwijtraak? Kan ik de gegevens herstellen? Kan Microsoft de gegevens herstellen?

De sleutel die wordt gebruikt om de back-upgegevens te versleutelen, is alleen beschikbaar op uw site. Micro soft onderhoudt geen kopie in Azure en heeft geen toegang tot de sleutel. Als u de sleutel kwijtraakt, kan Microsoft de back-upgegevens niet herstellen.

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

- [Veelgestelde vragen](backup-azure-vm-backup-faq.yml) over Azure VM-back-ups.
- [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md) over de Azure Backup-agent
