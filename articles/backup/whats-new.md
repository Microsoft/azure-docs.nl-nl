---
title: Wat is er nieuw in Azure Backup
description: Meer informatie over nieuwe functies in Azure Backup.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 68e0e5cc0876840c30ab9e428a2b96bd7d667756
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516328"
---
# <a name="whats-new-in-azure-backup"></a>Wat is er nieuw in Azure Backup

Azure Backup worden voortdurend verbeterd en nieuwe functies vrijgeven die de beveiliging van uw gegevens in Azure verbeteren. Met deze nieuwe functies breidt u uw gegevensbeveiliging uit naar nieuwe workloadtypen, verbetert u de beveiliging en verbetert u de beschikbaarheid van uw back-upgegevens. Ze voegen ook nieuwe beheer-, bewakings- en automatiseringsmogelijkheden toe.

U vindt meer informatie over de nieuwe releases door een bladwijzer te maken voor deze pagina of door u hier te abonneren [op updates.](https://azure.microsoft.com/updates/?query=backup)

## <a name="updates-summary"></a>Samenvatting van updates

- Maart 2021
  - [Azure Disk Backup is nu algemeen beschikbaar](#azure-disk-backup-is-now-generally-available)
  - [Back-upcentrum is nu algemeen beschikbaar](#backup-center-is-now-generally-available)
  - [Ondersteuning voor archieflagen voor Azure Backup (in preview)](#archive-tier-support-for-azure-backup-in-preview)
- Februari 2021
  - [Back-up voor Azure Blobs (in preview)](#backup-for-azure-blobs-in-preview)
- Januari 2021
  - [Azure Disk Backup (in preview)](#azure-disk-backup-in-preview)
  - [Versleuteling in rust met door de klant beheerde sleutels (algemene beschikbaarheid)](#encryption-at-rest-using-customer-managed-keys)
- November 2020
  - [Azure Resource Manager voor back-ups van Azure-bestands share (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Incrementele back-ups voor SAP HANA databases op azure-VM's (in preview)](#incremental-backups-for-sap-hana-databases-in-preview)
- September 2020
  - [Back-upcentrum (in preview)](#backup-center-in-preview)
  - [Back-Azure Database for PostgreSQL (in preview)](#backup-azure-database-for-postgresql-in-preview)
  - [Back-up en herstel van selectieve schijf](#selective-disk-backup-and-restore)
  - [Herstellen tussen regio's voor SQL Server en SAP HANA-databases op virtuele Azure-VM's (in preview)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Ondersteuning voor back-ups van VM's met maximaal 32 schijven (algemene beschikbaarheid)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Vereenvoudigde back-upconfiguratie voor SQL in Azure-VM's](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Back-SAP HANA in RHEL Azure Virtual Machines (in preview)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Zone-redundante opslag (ZRS) voor back-upgegevens (in preview)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Soft delete voor SQL Server en SAP HANA in Azure-VM's](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Azure Disk Backup is nu algemeen beschikbaar

Azure Backup biedt beheer van de levenscyclus van momentopnamen voor Azure Managed Disks door het periodiek maken van momentopnamen te automatiseren en deze te bewaren voor geconfigureerde duur met behulp van back-upbeleid.

Zie Overzicht van [Azure Disk Backup voor meer informatie.](disk-backup-overview.md)

## <a name="backup-center-is-now-generally-available"></a>Back-upcentrum is nu algemeen beschikbaar

Het Back-upcentrum vereenvoudigt het beheer van gegevensbeveiliging op schaal doordat u back-upbeheer vanaf één centrale console kunt ontdekken, beheren, bewaken, gebruiken en optimaliseren.

Zie Overzicht van Backup [Center voor meer informatie.](backup-center-overview.md)

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Ondersteuning voor archieflagen voor Azure Backup (in preview)

Azure Backup kunt u nu de kosten van back-ups voor langetermijnretentie verlagen met de beschikbaarheid van Archieflaag voor virtuele Azure-machines en SQL Server in virtuele Azure-machines.

Zie Ondersteuning voor archieflagen [(preview) voor meer informatie.](archive-tier-support.md)

## <a name="backup-for-azure-blobs-in-preview"></a>Back-up voor Azure Blobs (in preview)

Operationele back-up voor blobs is een beheerde, lokale oplossing voor gegevensbeveiliging waarmee u uw blok-blobs kunt beveiligen tegen verschillende scenario's voor gegevensverlies, zoals beschadigingen, blob-verwijderingen en onbedoeld verwijderen van opslagaccounts. De gegevens worden lokaal opgeslagen in het bronopslagaccount zelf en kunnen indien nodig worden hersteld naar een geselecteerd tijdstip. Het biedt dus een eenvoudige, veilige en rendabele manier om uw blobs te beveiligen.

Operationele back-up voor blobs kan worden geïntegreerd met Backup Center, naast andere mogelijkheden voor back-upbeheer, om één venster te bieden dat u kan helpen bij het beheren, bewaken, gebruiken en analyseren van back-ups op schaal.

Zie Overzicht van operationele [back-ups voor Azure Blobs (in preview) voor meer informatie.](blob-backup-overview.md)

## <a name="azure-disk-backup-in-preview"></a>Azure Disk Backup (in preview)

Azure Disk Backup biedt een gebruiksklare oplossing voor het beheer van de levenscyclus van momentopnamen voor [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) door het periodiek maken van momentopnamen te automatiseren en deze gedurende een geconfigureerde periode te bewaren met behulp van back-upbeleid. U kunt de momentopnamen van de schijf beheren zonder infrastructuurkosten en zonder aangepaste scripts of beheeroverhead. Dit is een crash-consistente back-upoplossing die een point-in-time back-up van een beheerde schijf maakt met behulp van [incrementele](../virtual-machines/disks-incremental-snapshots.md) momentopnamen met ondersteuning voor meerdere back-ups per dag. Het is ook een agent-less oplossing en heeft geen invloed op de prestaties van productietoepassing. Het biedt ondersteuning voor back-up en herstel van zowel besturingssysteem- als gegevensschijven (inclusief gedeelde schijven), ongeacht of ze momenteel zijn gekoppeld aan een virtuele Azure-machine die wordt uitgevoerd.

Zie Azure [Disk Backup (in preview) voor meer informatie.](disk-backup-overview.md)

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Versleuteling in rust met door de klant beheerde sleutels

Ondersteuning voor versleuteling-at-rest met behulp van door de klant beheerde sleutels is nu algemeen beschikbaar. Dit biedt u de mogelijkheid om de back-upgegevens in uw Recovery Services-kluizen te versleutelen met behulp van uw eigen sleutels die zijn opgeslagen in Azure Key Vaults. De versleutelingssleutel die wordt gebruikt voor het versleutelen van back-ups in de Recovery Services-kluis kan verschillen van de versleutelingssleutel die wordt gebruikt voor het versleutelen van de bron. De gegevens worden beveiligd met een op AES 256 gebaseerde gegevensversleutelingssleutel (DEK), die op zijn beurt wordt beveiligd met uw sleutels die zijn opgeslagen in de Key Vault. Vergeleken met versleuteling met behulp van door het platform beheerde sleutels (die standaard beschikbaar zijn), biedt dit u meer controle over uw sleutels en kunt u beter voldoen aan uw nalevingsbehoeften.

Zie Versleuteling van [back-upgegevens met door de klant beheerde sleutels voor meer informatie.](encryption-at-rest-with-cmk.md)

## <a name="azure-resource-manager-template-for-afs-backup"></a>Azure Resource Manager voor AFS-back-up

Azure Backup ondersteunt nu het configureren van back-ups voor bestaande Azure Azure Resource Manager sjabloon (ARM). Met de sjabloon configureert u de beveiliging voor een bestaande Azure-bestands share door de juiste details op te geven voor de Recovery Services-kluis en het back-upbeleid. Er wordt optioneel een nieuwe Recovery Services-kluis en een nieuw back-upbeleid gemaakt, en het opslagaccount met de bestandsshare wordt geregistreerd bij de Recovery Services-kluis.

Zie voor meer informatie Azure Resource Manager [sjablonen voor Azure Backup.](backup-rm-template-samples.md)

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Incrementele back-ups voor SAP HANA databases (in preview)

Azure Backup ondersteunt nu incrementele back-ups voor SAP HANA databases die worden gehost op Azure-VM's. Dit maakt snellere en rendabelere back-ups van uw SAP HANA mogelijk.

Zie verschillende opties die beschikbaar zijn [tijdens het](/sap-hana-faq-backup-azure-vm.yml#policy) maken van een back-upbeleid en hoe u een back-upbeleid maakt voor [SAP HANA databases.](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)

## <a name="backup-center-in-preview"></a>Back-upcentrum (in preview)

Azure Backup heeft een nieuwe systeemeigen beheermogelijkheid ingeschakeld voor het beheren van uw hele back-up van een centrale console. Backup Center biedt u de mogelijkheid om gegevensbeveiliging op schaal te bewaken, te beheren en te optimaliseren op een uniforme manier, consistent met de systeemeigen beheerervaring van Azure.

Met Backup Center krijgt u een geaggregeerde weergave van uw inventaris voor abonnementen, locaties, resourcegroepen, kluizen en zelfs tenants met behulp van Azure Lighthouse. Backup Center is ook een actiecentrum van waar u uw back-upactiviteiten kunt activeren, zoals het configureren van back-up, herstellen, maken van beleidsregels of kluizen, allemaal vanaf één plek. Bovendien kunt u met naadloze integratie Azure Policy uw omgeving en naleving bijhouden vanuit het perspectief van een back-up. Ingebouwde Azure-beleidsregels die specifiek zijn Azure Backup bieden u ook de mogelijkheid om back-ups op schaal te configureren.

Zie Overzicht van Backup [Center voor meer informatie.](backup-center-overview.md)

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Back-Azure Database for PostgreSQL (in preview)

Azure Backup en Azure Database Services zijn samen gekomen om een back-upoplossing van bedrijfsklasse te bouwen voor Azure PostgreSQL (nu in preview). U kunt nu voldoen aan uw gegevensbeschermings- en nalevingsbehoeften met een door de klant beheerd back-upbeleid waarmee back-ups maximaal tien jaar kunnen worden bewaren. Hiermee hebt u gedetailleerde controle over het beheren van de back-up- en herstelbewerkingen op het niveau van de afzonderlijke database. Op dezelfde manier kunt u eenvoudig herstellen tussen PostgreSQL-versies of blobopslag.

Zie back-up Azure Database for PostgreSQL [meer informatie.](backup-azure-database-postgresql.md)

## <a name="selective-disk-backup-and-restore"></a>Back-up en herstel van selectieve schijf

Azure Backup ondersteunt het maken van back-ups van alle schijven (besturingssysteem en gegevens) in een VM met behulp van de back-upoplossing voor virtuele machines. Nu kunt u met behulp van de functionaliteit voor back-up en herstel van selectieve schijven een back-up maken van een subset van de gegevensschijven in een virtuele machine. Dit biedt een efficiënte en rendabele oplossing voor uw back-up- en herstelbehoeften. Elk herstelpunt bevat alleen de schijven die zijn opgenomen in de back-upbewerking.

Zie Selective [disk backup and restore for Azure virtual machines (Selectief maken van schijfback-up en herstel voor virtuele Azure-machines) voor meer informatie.](selective-disk-backup-restore.md)

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Herstellen tussen regio's voor SQL Server en SAP HANA (in preview)

Met de introductie van herstel in verschillende regio's kunt u herstel naar eigen goed gevolg in een secundaire regio initiëren om echte downtimeproblemen in een primaire regio voor uw omgeving te beperken. Hierdoor wordt de secundaire regio volledig door de klant beheerd. Azure Backup maakt gebruik van de back-upgegevens die zijn gerepliceerd naar de secundaire regio voor dergelijke herstels.

Naast ondersteuning voor herstel in meerdere regio's voor virtuele Azure-machines, is de functie nu ook uitgebreid voor het herstellen van SQL- en SAP HANA-databases in virtuele Azure-machines.

Zie Herstel tussen regio's voor [SQL-databases](restore-sql-database-azure-vm.md#cross-region-restore) en Herstellen tussen regio's voor SAP HANA [databases voor meer informatie.](sap-hana-db-restore.md#cross-region-restore)

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Ondersteuning voor back-ups van VM's met maximaal 32 schijven

Tot nu toe Azure Backup ondersteunde 16 beheerde schijven per VM. Nu ondersteunt Azure Backup back-up van maximaal 32 beheerde schijven per VM.

Zie de ondersteuningsmatrix voor [VM-opslag voor meer informatie.](backup-support-matrix-iaas.md#vm-storage-support)

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Eenvoudigere back-upconfiguratie voor SQL in Azure-VM's

Het configureren van back-ups voor uw SQL Server in Azure-VM's is nu nog eenvoudiger met inline back-upconfiguratie geïntegreerd in het VM-deelvenster van de Azure Portal. In slechts enkele stappen kunt u back-ups van uw SQL Server om alle bestaande databases te beveiligen, evenals de databases die in de toekomst worden toegevoegd.

Zie Back-up maken van een SQL Server [vanuit het VM-deelvenster voor meer informatie.](backup-sql-server-vm-from-vm-pane.md)

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Back-SAP HANA in virtuele RHEL Azure-machines (in preview)

Azure Backup is de systeemeigen back-upoplossing voor Azure en is BackInt gecertificeerd door SAP. Azure Backup heeft nu ondersteuning toegevoegd voor Red Hat Enterprise Linux (RHEL), een van de meest gebruikte Linux-besturingssystemen met SAP HANA.

Zie de ondersteuningsmatrix voor SAP HANA [databaseback-upscenario voor meer informatie.](sap-hana-backup-support-matrix.md#scenario-support)

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Zone-redundante opslag (ZRS) voor back-upgegevens (in preview)

Azure Storage biedt een goede balans tussen hoge prestaties, hoge beschikbaarheid en hoge gegevens resiliency met de verschillende redundantieopties. Azure Backup kunt u deze voordelen ook uitbreiden naar back-upgegevens, met opties voor het opslaan van uw back-ups in lokaal redundante opslag (LRS) en geografisch redundante opslag (GRS). Er zijn nu aanvullende duurzaamheidsopties met de toegevoegde ondersteuning voor zone-redundante opslag (ZRS).

Zie Opslag redundantie instellen [voor de Recovery Services-kluis voor meer informatie.](backup-create-rs-vault.md#set-storage-redundancy)

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Soft delete voor SQL Server en SAP HANA workloads

De zorgen over beveiligingsproblemen, zoals malware, ransomware en inbraak, nemen toe. Deze beveiligingsproblemen kunnen kostbaar zijn, zowel wat geld als gegevens betreft. Ter bescherming tegen dergelijke aanvallen biedt Azure Backup beveiligingsfuncties voor het beveiligen van back-upgegevens, zelfs na verwijdering.

Een van deze functies is soft delete. Met een zachte verwijderperiode worden de back-upgegevens nog 14 dagen bewaard, zelfs als een kwaadwillende actor een back-up verwijdert (of als er per ongeluk back-upgegevens worden verwijderd), zodat dat back-upitem zonder gegevensverlies kan worden hersteld. De extra 14 dagen retentieperiode voor back-upgegevens met de status 'soft delete' heeft geen kosten voor u.

Nu worden, naast ondersteuning voor het verwijderen van virtuele Azure-SQL Server, SQL Server- en SAP HANA-workloads in Azure-VM's ook beveiligd met 'soft delete'.

Zie Voor meer informatie Soft [delete for SQL server in Azure VM (Soft Delete voor SQL Server in Azure VM) en SAP HANA in Azure VM-workloads.](soft-delete-sql-saphana-in-azure-vm.md)

## <a name="next-steps"></a>Volgende stappen

- [Azure Backup en best practices](guidance-best-practices.md)