---
title: Veelgestelde vragen over back-ups van Azure-schijven
description: Antwoorden vinden op veelgestelde vragen over Azure Disk Backup
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7729bc1120fc0e2f4361739a8e05f3a82ccb4268
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107309"
---
# <a name="frequently-asked-questions-about-azure-disk-backup"></a>Veelgestelde vragen over back-ups van Azure-schijven

In dit artikel vindt u antwoorden op veelgestelde vragen over back-ups van Azure-schijven. Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor meer informatie over de beschik baarheid van [Azure schijf back-](disk-backup-overview.md) upregio, ondersteunde scenario's en beperkingen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Kan ik een back-up van de schijf maken met behulp van de back-upoplossing van Azure schijf als er een back-up van de schijf van de virtuele machine wordt gemaakt met Azure

Azure Backup biedt ondersteuning naast elkaar voor het maken van een back-up van beheerde schijven met behulp van schijf back-ups en de back-upoplossingen van [Azure VM](backup-azure-vms-introduction.md) . Dit is handig wanneer u een toepassings consistente back-up van virtuele machines en frequentere back-ups van de besturingssysteem schijf of een specifieke gegevens schijf wilt maken, wat een crash consistent is zonder dat dit van invloed is op de prestaties van de productie toepassing.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Hoe kan ik de resource groep voor moment opnamen zoeken die ik heb gebruikt voor het configureren van de back-up voor een schijf?

In het scherm **back-upexemplaar** vindt u het veld momentopname resource groep in de sectie **Essentials** . U kunt het back-upexemplaar van de bijbehorende schijf zoeken en selecteren in Back-upcentrum of de back-upkluis.

![Resource groeps veld voor moment opname](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>Wat is een resource groep voor moment opnamen?

Azure Disk Backup biedt back-up van de operationele laag voor beheerde schijven. Dat wil zeggen dat de moment opnamen die zijn gemaakt tijdens de geplande en on-demand back-upbewerkingen, worden opgeslagen in een resource groep in uw abonnement. Azure Backup biedt direct herstel, omdat de incrementele moment opnamen worden opgeslagen in uw abonnement. Deze resource groep wordt de resource groep voor moment opnamen genoemd. Zie [back-up configureren](backup-managed-disks.md#configure-backup)voor meer informatie.

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Waarom moet de resource groep van de moment opname zich in hetzelfde abonnement benemen als van de schijf waarvan een back-up wordt gemaakt?

U kunt geen incrementele moment opname maken voor een bepaalde schijf buiten het abonnement van de schijf. Kies de resource groep in hetzelfde abonnement als die van de schijf waarvan een back-up moet worden gemaakt. Meer informatie over [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md#restrictions) voor beheerde schijven.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Waarom moet ik roltoewijzingen opgeven om back-ups te kunnen configureren, geplande en on-demand back-ups en herstel bewerkingen uit te voeren?

Azure Disk Backup maakt gebruik van de minimale bevoegdheids benadering voor het detecteren, beveiligen en herstellen van de beheerde schijven in uw abonnementen. Azure Backup gebruikt de beheerde identiteit van de [back-upkluis](backup-vault-overview.md) voor het verkrijgen van toegang tot andere Azure-bronnen. Een door het systeem toegewezen beheerde identiteit is beperkt tot één per resource en is verbonden met de levens cyclus van deze resource. U kunt machtigingen verlenen aan de beheerde identiteit door gebruik te maken van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Beheerde identiteit is een service-principal van een speciaal type dat alleen kan worden gebruikt met Azure-resources. Meer informatie over [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md). De back-upkluis heeft standaard geen machtiging voor toegang tot de schijf waarvan een back-up moet worden gemaakt, het maken van periodieke moment opnamen, het verwijderen van moment opnamen na de Bewaar periode en het herstellen van een schijf uit een back-up. Door roltoewijzingen expliciet te verlenen aan de beheerde identiteit van de back-upkluis, hebt u controle over het beheer van machtigingen voor de resources op de abonnementen.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Waarom wordt de Bewaar duur beperkt in het back-upbeleid?

Azure Disk Backup maakt gebruik van incrementele moment opnamen, die zijn beperkt tot 200 moment opnamen per schijf. Als u back-ups op aanvraag wilt maken, afgezien van geplande back-ups, beperkt het back-upbeleid de totale back-ups tot 180. Meer informatie over [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md#restrictions) voor beheerde schijven.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Hoe werkt de back-upfrequentie per uur en dagelijks in het back-upbeleid?

Azure Disk Backup biedt meerdere back-ups per dag. Als u vaker back-ups nodig hebt, kiest u de frequentie van de back-up **per uur** . De back-ups worden gepland op basis van het geselecteerde **tijds** interval. Als u bijvoorbeeld **elke vier uur** selecteert, worden de back-ups ongeveer elke vier uur gemaakt zodat de back-ups gelijkmatig over de hele dag worden gedistribueerd. Als back-up van de dag voldoende genoeg is, kiest u de frequentie van **dagelijkse** back-ups. Tijdens de dagelijkse back-upfrequentie kunt u het tijdstip van de dag opgeven waarop uw back-ups worden gemaakt. Het is belang rijk te weten dat de tijd van de dag de begin tijd van de back-up aangeeft en niet de tijd waarop de back-up is voltooid. De tijd die nodig is om de back-upbewerking te volt ooien, is afhankelijk van verschillende factoren, waaronder het verloop frequentie tussen opeenvolgende back-ups. Azure Disk Backup is echter een back-up zonder agent die gebruikmaakt van [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md) die geen invloed hebben op de prestaties van de productie-toepassing.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Waarom is de redundantie-instelling van de back-upkluis niet van toepassing op de back-ups die zijn opgeslagen in de operationele laag (de resource groep voor de moment opname)?

Azure Backup gebruikt [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md#restrictions) van Managed disks die alleen de Delta wijzigingen op schijven opslaan sinds de laatste moment opname op Standard-HDD opslag, ongeacht het opslag type van de bovenliggende schijf. Voor meer betrouw baarheid worden incrementele moment opnamen standaard op zone redundant Storage (ZRS) opgeslagen in regio's die ondersteuning bieden voor ZRS. Op dit moment ondersteunt Azure-schijf back-ups operationele back-ups van beheerde schijven waarbij de back-ups niet worden gekopieerd naar back-upkluis. De instelling back-upopslag redundantie van de back-upkluis is dus niet van toepassing op de herstel punten.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Kan ik Back-upcentrum gebruiken voor het configureren van back-ups en het beheren van back-upinstanties voor Azure-schijven?

Ja, Azure-schijf back-up is geïntegreerd in [Back-upcentrum](backup-center-overview.md). Dit biedt een **uniforme beheer ervaring** in azure voor ondernemingen om de back-ups op schaal te beheren, te controleren, te beheren en te analyseren. U kunt ook back-upkluis gebruiken om back-ups te maken, te herstellen en te beheren van de back-upinstanties die in de kluis worden beveiligd.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Waarom moet ik een back-upkluis maken en geen Recovery Services kluis gebruiken?

Een back-upkluis is een opslag entiteit in azure waarmee back-upgegevens worden opgeslagen voor bepaalde nieuwere werk belastingen die Azure Backup ondersteunt. U kunt back-upkluizen gebruiken voor het bewaren van back-upgegevens voor verschillende Azure-Services, zoals Azure Database for PostgreSQL-servers, Azure-schijven en nieuwere workloads die Azure Backup zullen ondersteunen. Back-upkluizen maken het eenvoudig om uw back-upgegevens te organiseren en zo de beheer overhead te minimaliseren. Raadpleeg [back-upkluizen](./backup-vault-overview.md) voor meer informatie.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>Kan er een back-up van de schijf worden gemaakt en moet de back-upkluis zich in verschillende abonnementen bevindt?

Ja, de bron-beheerde schijf waarvan een back-up moet worden gemaakt en de back-upkluis kan zich in verschillende abonnementen bevindt.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>Kan de schijf waarvan een back-up wordt gemaakt en de back-upkluis zich in verschillende regio's bevindt?

Nee, momenteel de bron-beheerde schijf waarvan een back-up moet worden gemaakt en de back-upkluis moeten zich in dezelfde regio bevinden.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>Kan ik een schijf in een ander abonnement herstellen?

Ja, u kunt de schijf herstellen naar een ander abonnement dan die van de bron-beheerde schijf waarvan de back-up wordt gemaakt.

### <a name="can-i-back-up-multiple-disks-together"></a>Kan ik een back-up maken van meerdere schijven?

Nee, punt-in-time moment opnamen van meerdere schijven die zijn gekoppeld aan een virtuele machine worden niet ondersteund. Zie voor meer informatie [back-ups configureren](backup-managed-disks.md#configure-backup) en meer informatie over beperkingen. Raadpleeg de [ondersteunings matrix](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Wat zijn mijn opties voor het maken van back-ups van schijven in meerdere abonnementen?

Op dit moment is het gebruik van de Azure Portal om een back-up van schijven te configureren beperkt tot Maxi maal 20 schijven van hetzelfde abonnement.

### <a name="what-is-a-target-resource-group"></a>Wat is een doel resource groep?

Tijdens een herstel bewerking kunt u het abonnement en een resource groep kiezen waarnaar u de schijf wilt herstellen. Azure Backup worden nieuwe schijven gemaakt op basis van het herstel punt in de geselecteerde resource groep. Dit wordt een doel resource groep genoemd. Houd er rekening mee dat de beheerde identiteit van de back-upkluis de roltoewijzing voor de doel resource groep nodig heeft om de herstel bewerking te kunnen uitvoeren. Zie de [documentatie over herstel](restore-managed-disks.md)voor meer informatie.

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Wat zijn de machtigingen die door Azure Backup worden gebruikt tijdens een back-up-en herstel bewerking?

Hieronder ziet u de acties die worden gebruikt in de rol van **schijf back-uplezer** toegewezen op de **schijf** waarvan een back-up moet worden gemaakt:

"Micro soft. Compute/schijven/lezen"

"Micro soft. Compute/disks/beginGetAccess/Action"

Hieronder ziet u de acties die worden gebruikt in de rol Inzender van de **schijf momentopname** die is toegewezen aan de **resource groep voor moment opnamen**:

"Micro soft. Compute/moment opnamen/verwijderen"

"Micro soft. Compute/moment opnamen/schrijven"

"Micro soft. Compute/snap shots/lezen"

"Micro soft. Storage/Storage accounts/schrijven"

"Micro soft. Storage/Storage accounts/lezen"

"Micro soft. Storage/Storage accounts/verwijderen"

"Micro soft. resources/abonnementen/resourceGroups/lezen"

"Micro soft. Storage/Storage accounts/listkeys ophalen/Action"

"Micro soft. Compute/moment opnamen/beginGetAccess/actie"

"Micro soft. Compute/moment opnamen/endGetAccess/actie"

"Micro soft. Compute/disks/beginGetAccess/Action"

Hieronder ziet u de acties die worden gebruikt in de rol **schijf herstel operator** die is toegewezen aan de **doel resource groep**:

"Micro soft. Compute/disks/Write"

"Micro soft. Compute/schijven/lezen"

"Micro soft. resources/abonnementen/resourceGroups/lezen"

>[!NOTE]
>De machtigingen voor deze rollen kunnen in de toekomst worden gewijzigd op basis van de functies die worden toegevoegd door de Azure Backup service.

## <a name="next-steps"></a>Volgende stappen

- [Azure Disck Backup-ondersteuningsmatrix](disk-backup-support-matrix.md)