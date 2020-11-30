---
title: 'Veelgestelde vragen: back-ups maken van virtuele Azure-machines'
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het maken van back-ups van virtuele Azure-machines met de Azure Backup-service.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 0f4f990654cc23fde7cf1ad2e37ba1ada76d94e3
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324785"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Veelgestelde vragen: back-ups maken van virtuele Azure-machines

In dit artikel vindt u antwoorden op veelgestelde vragen over het maken van back-ups van virtuele Azure-machines met de [Azure backup](./backup-overview.md) -service.

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Welke VM-installatie kopieën kunnen worden ingeschakeld voor back-up wanneer ik ze Maak?

Wanneer u een virtuele machine maakt, kunt u back-ups inschakelen voor Vm's met [ondersteunde besturings systemen](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="why-initial-backup-is-taking-lot-of-time-to-complete"></a>Waarom duurt het lang om een eerste back-up te maken?

De eerste back-up is altijd een volledige back-up en is afhankelijk van de grootte van de gegevens en wanneer de back-up wordt verwerkt. <br>
Zie [Aanbevolen procedures voor back-](./backup-azure-vms-introduction.md#best-practices)ups om de back-upprestaties te verbeteren. [Overwegingen voor back-ups](./backup-azure-vms-introduction.md#backup-and-restore-considerations) en [back-upprestaties](./backup-azure-vms-introduction.md#backup-performance)<br>
Hoewel de totale back-uptijd voor incrementele back-ups minder dan 24 uur is, is dit mogelijk niet het geval voor de eerste back-up.

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Worden de kosten voor back-ups opgenomen in de VM-kosten?

Nee. De back-upkosten zijn gescheiden van de kosten van de virtuele machine. Meer informatie over [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Welke machtigingen zijn vereist voor het inschakelen van een back-up voor een virtuele machine?

Als u een VM-bijdrager bent, kunt u back-up inschakelen op de VM. Als u een aangepaste rol gebruikt, hebt u de volgende machtigingen nodig voor het inschakelen van back-ups op de VM:

- Micro soft. Recovery Services/kluizen/schrijven
- Micro soft. Recovery Services/kluizen/lezen
- Micro soft. Recovery Services/locaties/*
- Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/*/Read
- Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/lezen
- Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/write
- Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/schrijven
- Micro soft. Recovery Services/kluizen/backupPolicies/lezen
- Micro soft. Recovery Services/kluizen/backupPolicies/schrijven

Als uw Recovery Services kluis en VM verschillende resource groepen hebben, moet u ervoor zorgen dat u schrijf machtigingen hebt in de resource groep voor de Recovery Services kluis.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Maakt een back-uptaak op aanvraag gebruik van hetzelfde Bewaar schema als geplande back-ups?

Nee. Geef de Bewaar termijn op voor een back-uptaak op aanvraag. Standaard wordt deze 30 dagen bewaard bij het activeren van de portal.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ik heb onlangs Azure Disk Encryption ingeschakeld op een aantal virtuele machines. Worden mijn back-ups gewoon uitgevoerd?

Geef machtigingen voor Azure Backup op voor toegang tot de Key Vault. Geef de machtigingen in Power shell op zoals beschreven in de sectie **back-up inschakelen** in de [Azure backup Power shell](backup-azure-vms-automation.md) -documentatie.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Ik heb VM-schijven naar Managed disks gemigreerd. Worden mijn back-ups gewoon uitgevoerd?

Ja, back-ups werken probleemloos. U hoeft niets opnieuw te configureren.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Waarom zie ik mijn VM niet in de wizard voor Back-up configureren?

De wizard bevat alleen Vm's in dezelfde regio als de kluis en er wordt nog geen back-up van gemaakt.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Mijn VM is afgesloten. Werkt een on-demand of een geplande back-up?

Ja. Back-ups worden uitgevoerd wanneer een machine wordt afgesloten. Het herstel punt is gemarkeerd als crash consistent.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan ik een actieve back-uptaak annuleren?

Ja. U kunt de back-uptaak annuleren in een status van het maken van de **moment opname** . U kunt een taak niet annuleren als de gegevens overdracht van de moment opname wordt uitgevoerd.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Ik heb een vergren deling ingeschakeld voor de resource groep die is gemaakt door Azure Backup Service (bijvoorbeeld `AzureBackupRG_<geo>_<number>` ). Worden mijn back-ups gewoon uitgevoerd?

Als u de resource groep die is gemaakt door de Azure Backup-Service vergrendelt, mislukken back-ups als er een maximum limiet van 18 herstel punten is.

Verwijder de vergren deling en wis de herstel punt verzameling van die resource groep om de toekomstige back-ups te kunnen volt ooien. [Volg deze stappen](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) om de verzameling met herstel punten te verwijderen.

### <a name="i-have-a-lock-at-the-resource-group-level-that-contains-all-the-resources-related-to-my-virtual-machine-will-my-backup-work"></a>Ik heb een vergren deling op het niveau van de resource groep met alle resources die betrekking hebben op mijn virtuele machine. Werkt mijn back-up?

Azure Backup maakt een afzonderlijke resource groep in de indeling `AzureBackupRG_<geo>_<number>` om ResourcePointCollections-objecten op te slaan. Omdat deze resource groep eigendom is van de service, kan het vergren delen van back-ups mislukken. Vergren delingen kunnen alleen worden toegepast op resource groepen die door klanten zijn gemaakt.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Ondersteunt Azure Backup standaard SSD-Managed disks?

Ja, Azure Backup ondersteunt [Standard SSD Managed disks](../virtual-machines/disks-types.md#standard-ssd).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Kan ik een back-up maken van een VM met een Write Accelerator (WA) ingeschakelde schijf?

Moment opnamen kunnen alleen worden gemaakt op gegevens schijven die WA zijn ingeschakeld en geen besturingssysteem schijven. Alleen gegevens schijven die WA zijn ingeschakeld, kunnen worden beveiligd.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Ik heb een virtuele machine met Write Accelerator (WA) schijven en SAP HANA geïnstalleerd. Hoe kan ik back-up?

Azure Backup kunt een back-up maken van de gegevens schijf met WA-functionaliteit. De back-up biedt echter geen consistentie van de data base.

Azure Backup biedt een streaming-back-upoplossing voor SAP HANA-data bases met een RPO van 15 minuten. Het is Backint gecertificeerd door SAP om een systeem eigen back-upondersteuning te bieden die gebruikmaakt van de systeem eigen Api's van SAP HANA. Meer informatie [over het maken van back-ups van SAP Hana-data bases in azure-vm's](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Wat is de maximale vertraging die ik kan verwachten in de start tijd van de back-up vanaf de geplande back-uptijd die ik heb ingesteld in mijn VM-back-upbeleid?

De geplande back-up wordt binnen twee uur na de geplande back-uptijd geactiveerd. Als 100 Vm's bijvoorbeeld de begin tijd van de back-up hebben gepland om 2:00 uur, wordt de back-uptaak uitgevoerd op basis van 4:00 uur op de laatste alle 100 Vm's. Als geplande back-ups zijn onderbroken vanwege een storing en hervat of opnieuw worden geprobeerd, kan de back-up worden gestart buiten dit geplande venster van twee uur.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Wat is de mini maal toegestane Bewaar termijn voor een dagelijks back-uppunt?

Het back-upbeleid van Azure Virtual Machine ondersteunt een minimale Bewaar termijn van zeven dagen tot 9999 dagen. Voor elke wijziging in een bestaand back-upbeleid voor de VM met minder dan zeven dagen is een update vereist om te voldoen aan de minimale Bewaar termijn van zeven dagen.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Wat gebeurt er als ik het hoofdletter gebruik Wijzig voor de naam van mijn VM of mijn VM-resource groep?

Als u het hoofdletter gebruik (in boven of beneden) van uw virtuele machine of VM-resource groep wijzigt, verandert de naam van het back-upitem niet. Dit is echter normaal Azure Backup gedrag. De wijziging in de situatie wordt niet weer gegeven in het back-upitem, maar wordt bijgewerkt op de back-end.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Kan ik back-ups maken of herstellen van selectieve schijven die zijn gekoppeld aan een VM?

Azure Backup ondersteunt nu selectieve back-up en herstel met behulp van de back-upoplossing van Azure virtual machine. Zie [selectief schijf back-ups maken en herstellen voor Azure-vm's](selective-disk-backup-restore.md)voor meer informatie.

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>Worden beheerde identiteiten behouden als er een Tenant wijziging optreedt tijdens het maken van een back-up?

Als er wijzigingen in de [Tenant](/azure/devops/organizations/accounts/change-azure-ad-connection) optreden, moet u [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md) uitschakelen en opnieuw inschakelen om back-ups opnieuw te laten werken.

## <a name="restore"></a>Herstellen

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hoe kan ik bepalen of u alleen schijven of een volledige virtuele machine wilt herstellen?

U kunt een VM-herstel bewerking beschouwen als een snelle optie voor het maken van een virtuele Azure-machine. Met deze optie wijzigt u de schijf namen, containers die worden gebruikt door de schijven, open bare IP-adressen en netwerk interface namen. De wijziging houdt unieke resources bij wanneer een virtuele machine wordt gemaakt. De virtuele machine is niet toegevoegd aan een beschikbaarheidsset.

U kunt de optie voor het herstellen van de schijf gebruiken als u het volgende wilt doen:

- Pas de virtuele machine aan die wordt gemaakt. Wijzig bijvoorbeeld de grootte.
- Configuratie-instellingen toevoegen die niet aanwezig zijn op het moment van de back-up.
- Bepaal de naamgevings regels voor resources die worden gemaakt.
- Voeg de virtuele machine toe aan een beschikbaarheidsset.
- Voeg een andere instelling toe die moet worden geconfigureerd met Power shell of een sjabloon.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kan ik back-ups van niet-beheerde VM-schijven herstellen na een upgrade naar Managed disks?

Ja, u kunt back-ups gebruiken die zijn gemaakt voordat schijven zijn gemigreerd van niet-beheerd naar beheerd.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hoe herstel ik een VM naar een herstelpunt vóór de migratie van de VM naar beheerde schijven?

Het herstel proces blijft hetzelfde. Als het herstel punt een bepaald tijdstip heeft wanneer de virtuele machine niet-beheerde schijven bevat, kunt u de [schijven herstellen als](tutorial-restore-disk.md#unmanaged-disks-restore)niet-beheerd. Als de virtuele machine schijven heeft beheerd, kunt u [schijven herstellen als managed disks](tutorial-restore-disk.md#managed-disk-restore). Vervolgens kunt u [een virtuele machine maken op basis van die schijven](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

Meer [informatie hierover vindt](backup-azure-vms-automation.md#restore-an-azure-vm) u in Power shell.

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Als de virtuele machine niet kan worden gemaakt met de herstel bewerking, wat gebeurt er dan met de schijven die zijn opgenomen in het herstellen?

In het geval van een beheerde VM-herstel bewerking, zelfs als het maken van de virtuele machine mislukt, worden de schijven nog steeds hersteld.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>Kan ik een verwijderde VM herstellen?

Ja. Zelfs als u de virtuele machine verwijdert, kunt u naar het bijbehorende back-upitem in de kluis gaan en het herstellen vanaf een herstel punt.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Hoe kan ik een VM naar dezelfde beschikbaarheids sets herstellen?

Voor virtuele Azure-machines met beheerde schijven wordt het herstellen naar de beschikbaarheids sets ingeschakeld door een optie in de sjabloon op te geven tijdens het herstellen als beheerde schijven. Deze sjabloon bevat de invoer parameter met de naam **beschikbaarheids sets**.

### <a name="how-do-we-get-faster-restore-performances"></a>Hoe worden de prestaties sneller teruggezet?

Met de functie voor [direct terugzetten](backup-instant-restore-capability.md) kunt u sneller back-ups maken en direct herstellen vanuit de moment opnamen.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Wat gebeurt er wanneer de sleutel kluis instellingen voor de versleutelde VM worden gewijzigd?

Nadat u de sleutel kluis instellingen voor de versleutelde virtuele machine hebt gewijzigd, blijven back-ups werken met de nieuwe set details. Na het herstellen van een herstel punt vóór de wijziging, moet u echter de geheimen in een sleutel kluis herstellen voordat u de virtuele machine kunt maken. Zie dit [artikel](./backup-azure-restore-key-secret.md) voor meer informatie.

Voor bewerkingen zoals geheime/sleutel rollen is deze stap niet vereist en dezelfde sleutel kluis kan na het herstellen worden gebruikt.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Kan ik toegang krijgen tot de virtuele machine nadat deze is hersteld vanwege een verbroken relatie met de domein controller?

Ja, u kunt de VM na het herstellen openen als gevolg van een virtuele machine met een verbroken relatie met de domein controller. Raadpleeg dit [artikel](./backup-azure-arm-restore-vms.md#post-restore-steps) voor meer informatie

### <a name="why-restore-operation-is-taking-long-time-to-complete"></a>Waarom duurt mijn herstelbewerking zo lang?

De totale herstel tijd is afhankelijk van de invoer/uitvoer-bewerkingen per seconde (IOPS) en de door Voer van het opslag account. De totale herstel tijd kan worden beïnvloed als het doel-opslag account is geladen met andere Lees-en schrijf bewerkingen van toepassingen. Als u de herstel bewerking wilt verbeteren, selecteert u een opslag account dat niet is geladen met andere toepassings gegevens.

## <a name="manage-vm-backups"></a>Back-ups van uw virtuele machine beheren

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Wat gebeurt er als ik een back-upbeleid Wijzig?

Er wordt een back-up van de virtuele machine gemaakt met behulp van de instellingen voor planning en bewaren in het gewijzigde of nieuwe beleid.

- Als de retentie is uitgebreid, worden de bestaande herstel punten gemarkeerd en bewaard op basis van het nieuwe beleid.
- Als de retentie wordt beperkt, worden herstel punten gemarkeerd voor verwijdering in de volgende opschoon taak en vervolgens verwijderd.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hoe kan ik een VM waarvan een back-up is gemaakt door Azure Backup naar een andere resource groep?

1. De back-up tijdelijk stoppen en back-upgegevens behouden.
2. Voer de volgende stappen uit om virtuele machines te verplaatsen die zijn geconfigureerd met Azure Backup:

   1. Zoek de locatie van de virtuele machine.
   2. Een resource groep zoeken met het volgende naamgevings patroon: `AzureBackupRG_<location of your VM>_1` . Bijvoorbeeld *AzureBackupRG_westus2_1*
   3. Selecteer in het Azure Portal de optie **verborgen typen weer geven**.
   4. Zoek de resource met het type **micro soft. Compute/restorePointCollections** die het naamgevings patroon heeft `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.
   6. Nadat de bewerking delete is voltooid, kunt u de virtuele machine verplaatsen.

3. Verplaats de virtuele machine naar de doel resource groep.
4. Hervat de back-up.

U kunt de virtuele machine herstellen vanaf beschik bare herstel punten die zijn gemaakt voor de verplaatsings bewerking.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>Wat gebeurt er wanneer ik een VM Verplaats naar een andere resource groep?

Zodra een virtuele machine naar een andere resource groep is verplaatst, is het een nieuwe virtuele machine, voorzover Azure Backup betrokken is.

Nadat u de VM naar een nieuwe resource groep hebt verplaatst, kunt u de virtuele machine opnieuw beveiligen in dezelfde kluis of een andere kluis. Aangezien dit een nieuwe VM is voor Azure Backup, wordt deze afzonderlijk in rekening gebracht.

De oude herstel punten van de VM kunnen zo nodig worden teruggezet. Als u deze back-upgegevens niet nodig hebt, kunt u stoppen met het beveiligen van uw oude VM met gegevens verwijderen.

### <a name="is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy"></a>Geldt er een limiet voor het aantal Vm's dat kan worden gekoppeld aan hetzelfde back-upbeleid?

Ja, er is een limiet van 100 Vm's die kunnen worden gekoppeld aan hetzelfde back-upbeleid vanuit de portal. We raden u aan meer dan 100 Vm's te maken meerdere back-upbeleid met dezelfde planning of een ander schema.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Hoe kan ik de Bewaar instellingen voor mijn back-ups weer geven?

Op dit moment kunt u de Bewaar instellingen weer geven op het niveau van het back-upitem (VM) op basis van het back-upbeleid dat is toegewezen aan de virtuele machine.

Een van de manieren om de Bewaar instellingen voor uw back-ups weer te geven, is door te navigeren naar het [dash board](./backup-azure-manage-vms.md#view-vms-on-the-dashboard) back-upitem voor uw virtuele machine in de Azure Portal. Als u de koppeling naar het back-upbeleid selecteert, kunt u de Bewaar periode van alle dagelijkse, wekelijkse, maandelijkse en jaarlijkse Bewaar punten bekijken die zijn gekoppeld aan de virtuele machine.

U kunt ook [Backup Explorer](./monitor-azure-backup-with-backup-explorer.md) gebruiken om de Bewaar instellingen voor al uw virtuele machines te bekijken binnen één deel venster. Ga vanuit een Recovery Services kluis naar back-up Verkenner, ga naar het tabblad **Back-upitems** en selecteer de geavanceerde weer gave om gedetailleerde informatie over de Bewaar periode voor elke VM weer te geven.