---
title: Azure Backup woordenlijst
description: In dit artikel worden de termen gedefinieerd die nuttig zijn voor gebruik met Azure Backup.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 121258665ab275fdcffd618e7c0cf1b3e0537e70
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661473"
---
# <a name="azure-backup-glossary"></a>Azure Backup woordenlijst

Deze verklarende woorden lijst kan handig zijn bij het gebruik van Azure Backup.

>[!NOTE]
>
> - Termen die zijn gemarkeerd met het voor voegsel ' (workload-specifieke term) ' verwijzen naar de termen die alleen relevant zijn in de context van een specifieke subset van werk belastingen die Azure Backup ondersteunt.
> - Voor termen die vaak in Azure Backup documentatie worden gebruikt, maar die verwijzen naar andere Azure-Services, wordt een directe koppeling naar de documentatie van de relevante Azure-service geleverd.

## <a name="afs-azure-file-shares"></a>AFS (Azure-bestands shares)

Raadpleeg de [documentatie van Azure files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

## <a name="alternate-location-recovery"></a>Herstel naar een andere locatie

Een herstel bewerking die vanaf het herstel punt is uitgevoerd naar een andere locatie dan de oorspronkelijke locatie waar de back-ups zijn gemaakt. Wanneer u back-ups van Azure-VM'S gebruikt, wordt de virtuele machine teruggezet op een andere server dan de oorspronkelijke server waar de back-ups zijn gemaakt. Wanneer u een back-up van Azure file share gebruikt, betekent dit dat de gegevens worden teruggezet naar een bestands share die afwijkt van de back-up van de bestands share.

## <a name="application-consistent-backup"></a>Toepassings consistente back-up

(Werk belasting-specifieke termijn)

Toepassings consistente back-ups nemen geheugen inhoud en I/O-bewerkingen in behandeling. App-consistente moment opnamen maken gebruik van een [VSS](#vss-windows-volume-shadow-copy-service) -VSS Writer (of pre of post scripts voor Linux) om de consistentie van de app-gegevens te garanderen voordat een back-up wordt uitgevoerd. [Meer informatie](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>ARM-sjablonen (Azure Resource Manager)

Raadpleeg de [documentatie van arm-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview).

## <a name="autoprotection-for-databases"></a>Autoprotection (voor data bases)

(Werk belasting-specifieke termijn)

Automatische beveiliging is een mogelijkheid waarmee u automatisch alle data bases in een zelfstandig SQL Server exemplaar of een SQL Server AlwaysOn-beschikbaarheids groep kunt beveiligen. Hiermee worden niet alleen back-ups voor de bestaande data bases ingeschakeld, maar ook alle data bases die u in de toekomst kunt toevoegen, worden beveiligd.

## <a name="availability-storage-replication-types"></a>Beschik baarheid (typen opslag replicatie)

Azure Backup biedt drie typen replicatie om uw opslag en gegevens Maxi maal beschikbaar te stellen:

### <a name="lrs"></a>LRS

Met [lokaal redundante opslag (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage) worden uw back-upgegevens drie keer gerepliceerd (er worden drie kopieën van uw back-upgegevens gemaakt) in een opslag schaal eenheid in een Data Center. Alle kopieën van de back-upgegevens bevinden zich in dezelfde regio. LRS is een goedkope optie voor het beveiligen van uw back-upgegevens tegen lokale hardwarefouten.

### <a name="grs"></a>GRS

[Geografisch redundante opslag (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) is de standaardinstelling en is de replicatieoptie die wordt aanbevolen. GRS repliceert uw back-upgegevens naar een secundaire regio, honderden kilo meters van de primaire locatie van de bron gegevens. GRS kost meer dan LRS, maar GRS biedt een hoger duurzaamheids niveau voor uw back-upgegevens, zelfs als er sprake is van een regionale storing.

>[!NOTE]
>Voor GRS-kluizen waarvoor de functie voor het terugzetten van meerdere regio's is ingeschakeld, wordt back-upopslag geüpgraded van GRS naar RA-GRS (Geo-Redundant opslag met lees toegang).

### <a name="zrs"></a>ZRS

[Zone-redundante opslag (ZRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#zone-redundant-storage) repliceert uw back-upgegevens in [beschikbaarheids zones](https://docs.microsoft.com/azure/availability-zones/az-overview#availability-zones), waarbij de back-upgegevens locatie en tolerantie in dezelfde regio worden gegarandeerd. Daarom kan een back-up worden gemaakt van uw kritieke workloads waarvoor [gegevens locatie](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) vereist zijn in ZRS.

## <a name="azure-command-line-interface-cli"></a>Azure-opdrachtregelinterface (CLI)

Raadpleeg de [documentatie van Azure cli](https://docs.microsoft.com/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Raadpleeg de [documentatie van Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview).

## <a name="azure-powershell"></a>Azure PowerShell

Raadpleeg de [documentatie van Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Raadpleeg de [documentatie van Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

## <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Raadpleeg de [documentatie van Azure Disk Encryption](https://docs.microsoft.com/azure/security/fundamentals/azure-disk-encryption-vms-vmss).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Back-end opslag/Cloud opslag/back-upopslag

De daad werkelijke opslag die wordt gebruikt door een back-upexemplaar. Bevat de grootte van alle Bewaar punten die voor het back-upexemplaar bestaan (zoals gedefinieerd door het back-up-en bewaar beleid).

## <a name="bare-metal-backup"></a>Bare metal-back-up

Back-ups maken van bestanden van het besturings systeem en alle gegevens op essentiële volumes, met uitzonde ring van gebruikers gegevens. Een bare metal-back-up bevat per definitie een systeem status back-up. Het biedt beveiliging wanneer een computer niet start en u moet alles herstellen. [Meer informatie](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>Back-upextensies/VM-extensies

(Specifiek voor het type Azure VM-werk belasting)

Extensies van virtuele Azure-machines (VM's) zijn kleine toepassingen die configuratie na de implementatie en automatiseringstaken voor Azure-VM's bieden. Azure Backup maakt back-ups van virtuele Azure-machines door een uitbrei ding te installeren in de Azure VM-agent die op de computer wordt uitgevoerd. Azure Backup deze uitbrei dingen automatisch beheert en gebruikers hoeven deze extensies niet hand matig bij te werken.

## <a name="backup-instance--backup-item"></a>Back-upexemplaar/back-upitem

Een gegevens bron waarvan een back-up wordt gemaakt naar een kluis met een bepaald back-upbeleid en een Bewaar proces, vormt een back-upexemplaar of een back-upitem.

## <a name="backup-rule--backup-policy"></a>Back-upregel/back-upbeleid

Een back-upregel is een door de gebruiker gedefinieerde regel waarmee wordt aangegeven wanneer en hoe vaak back-ups moeten worden uitgevoerd op een gegevens bron. Voor sommige werkbelasting typen biedt back-upbeleid ook een manier om de momentopname methode op te geven die moet worden toegepast op de gegevens bron (volledig, incrementeel, differentieel). Back-upbeleid wordt vaak gemaakt als een combi natie van back-upregels en bewaar regels.

## <a name="backup-vault"></a>Back-upkluis

Een Azure Resource Manager resource van het type *micro soft. DataProtection/BackupVaults*. Op dit moment worden back-upkluizen gebruikt om back-ups te maken van Azure-data bases voor PostgreSQL server. Meer [informatie over back-upkluizen](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (bedrijfs continuïteit en nood herstel)

BCDR omvat een set processen die een organisatie nodig heeft om ervoor te zorgen dat apps en workloads actief zijn tijdens geplande en ongeplande service of Azure-uitval, met minimale bedrijfs onderbrekingen. Meer [informatie](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) over de verschillende services die Azure biedt om u te helpen bij het maken van een strategie voor een BCDR.

## <a name="churn"></a>Verloop

Het percentage wijziging van de gegevens waarvan een back-up wordt gemaakt tussen twee opeenvolgende back-ups. Dit kan worden veroorzaakt door het toevoegen van nieuwe gegevens of het wijzigen of verwijderen van bestaande gegevens.

## <a name="crash-consistent-backup"></a>Crash consistente back-up

(Werk belasting-specifieke termijn)

Crash-consistente moment opnamen doen zich meestal voor als een Azure-VM wordt afgesloten op het moment van de back-up. Alleen de gegevens die al op de schijf aanwezig zijn op het moment van de back-up worden vastgelegd en er een back-up van wordt gemaakt. [Meer informatie](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>Meerdere regio's herstellen (CRR)

Als een van de [Opties voor terugzetten](backup-azure-arm-restore-vms.md#restore-options)met behulp van cross Region Restore (CRR) kunt u back-upitems herstellen in een secundaire regio, een [Azure-gekoppelde regio](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

## <a name="data-box"></a>Data box

Raadpleeg de [documentatie van het data Box](https://docs.microsoft.com/azure/databox/data-box-overview).

## <a name="datasource"></a>Gegevensbron

Een resource (Azure-resource, proxy bron of on-premises resource) die een kandidaat is voor het maken van een back-up. Bijvoorbeeld een Azure-VM of een Azure-bestands share.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(Werk belasting-specifieke termijn)

Raadpleeg de [documentatie van DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

Raadpleeg de [ExpressRoute-documentatie](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

## <a name="file-system-consistent-backup"></a>Bestandssysteem consistente back-up

(Werk belasting-specifieke termijn)

Bestandssysteem consistente back-ups bieden consistentie door een moment opname van alle bestanden tegelijk te maken. [Meer informatie](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>Front-end opslag/bron grootte

De grootte van de gegevens waarvan een back-up moet worden gemaakt voor een gegevens bron. De frontend-grootte van een gegevens bron bepaalt het aantal [beveiligde instanties](#protected-instance) .

## <a name="full-backup"></a>Volledige back-up

In volledige back-ups wordt een kopie van de hele gegevens bron opgeslagen voor elke back-up.

## <a name="gfs-backup-policy"></a>GFS-back-upbeleid

Een GFS-back-upbeleid (groot vader-vader-zoon) is een bestand waarmee u wekelijks, maandelijks en jaarlijks back-upschema kunt definiëren naast het dagelijks back-upschema. De wekelijkse back-ups zijn bekend als ' Sons ', de maandelijkse back-ups worden ' vaders ' genoemd en de jaarlijkse back-ups worden ' Grandfathers ' genoemd. Elk van deze sets van back-upkopieën kan worden geconfigureerd om te worden bewaard gedurende een bepaalde periode, waardoor de retentie opties voor back-upkopieën beter kunnen worden aangepast. GFS-beleid is handig voor het bewaren van back-ups gedurende een lange periode op een meer opslag efficiënte manier.

## <a name="iaas-vms--azure-vms"></a>IaaS Vm's/Azure Vm's

Raadpleeg de [documentatie van Azure VM](https://docs.microsoft.com/azure/virtual-machines/).

## <a name="incremental-backup"></a>Incrementele back-up

Incrementele back-ups slaan alleen de blokken op die zijn gewijzigd sinds de vorige back-up.

## <a name="instant-restore"></a>Direct terugzetten

(Specifieke werkbelasting voorwaarde) Bij direct terugzetten moet u een computer rechtstreeks herstellen vanuit de back-upmomentopname in plaats van de kopie van de moment opname in de kluis. Onmiddellijke herstel bewerkingen zijn sneller dan het terugzetten vanuit een kluis. Het aantal direct herstel punten dat beschikbaar is, is afhankelijk van de retentie duur die voor moment opnamen is geconfigureerd. Momenteel alleen van toepassing op back-ups van Azure VM.

## <a name="iops"></a>IOPS

Invoer/uitvoer-bewerkingen per seconde.

## <a name="item-level-restore"></a>Herstel op item niveau

(Werk belasting-specifieke termijn)

Herstellen van afzonderlijke bestanden of mappen in de computer vanaf het herstel punt.

## <a name="job"></a>Taak

Een back-uptaak die is gemaakt door een gebruiker of de Azure Backup service. Taken kunnen gepland of on-demand (ad-hoc) zijn. Er zijn verschillende typen taken: back-up, herstel, beveiliging configureren, enzovoort. [Meer informatie over taken](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

## <a name="mabs--azure-backup-server"></a>MABS/Azure Backup Server

(Werk belasting-specifieke termijn)

Met Azure Backup Server kunt u werk belastingen van toepassingen, zoals virtuele machines van Hyper-V, Microsoft SQL Server, share Point server, micro soft Exchange en Windows-clients beveiligen vanaf één console. Het neemt veel van de back-up van de werk belasting over van DPM, maar met enkele verschillen. [Meer informatie](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Managed Disks

Raadpleeg de [documentatie voor Managed disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview).

## <a name="mars-agent"></a>MARS-agent

(Werk belasting-specifieke termijn)

De MARS-agent wordt ook wel **Azure backup agent** -of **Recovery Services-agent** genoemd en wordt door Azure Backup gebruikt voor het maken van back-ups van gegevens van on-premises machines en Azure-vm's naar een back-up Recovery Services kluis in Azure. [Meer informatie](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>NSG (netwerk beveiligings groep)

Raadpleeg de [NSG-documentatie](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview).

## <a name="offline-seeding"></a>Offline seeding

Offline seeding verwijst naar het proces van het overdragen van de initiële (volledige) back-up offline, zonder het gebruik van netwerk bandbreedte. Het biedt een mechanisme voor het kopiëren van back-upgegevens naar fysieke opslag apparaten, die vervolgens worden verzonden naar een nabijgelegen Azure-Data Center en geüpload naar een Recovery Services kluis. [Meer informatie](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>Back-ups op aanvraag/ad hoc-back-up

Een back-uptaak die wordt geactiveerd door een gebruiker op een eenmalige basis en niet op basis van het back-upschema (beleid) dat is geconfigureerd voor de resource.

## <a name="original-location-recovery-olr"></a>Herstel van de oorspronkelijke locatie (herstellen)

Een herstel bewerking die vanaf het herstel punt is uitgevoerd naar de bron locatie van waaruit de back-ups zijn gemaakt, vervangen door de status die is opgeslagen in het herstel punt. Wanneer u back-ups van Azure-VM'S gebruikt, wordt de virtuele machine teruggezet naar de oorspronkelijke server waarop de back-ups zijn gemaakt. Wanneer u een back-up van Azure file share gebruikt, betekent dit dat de gegevens worden teruggezet naar de bestands share waarvan een back-up is gemaakt.

## <a name="passphrase"></a>Wachtzin

(Werk belasting-specifieke termijn)

Een wachtwoordzin wordt gebruikt voor het versleutelen en ontsleutelen van gegevens tijdens het maken van een back-up of het herstellen van uw on-premises of lokale computer met de MARS-agent naar of van Azure.

## <a name="private-endpoint"></a>Privé-eindpunt

Raadpleeg de [documentatie over het persoonlijke eind punt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

## <a name="protected-instance"></a>Beveiligd exemplaar

Een beveiligd exemplaar verwijst naar de computer, fysieke of virtuele server die u gebruikt voor het configureren van de back-up naar Azure.  Vanuit het **oogpunt van facturering** is het aantal beveiligde instanties voor een machine een functie van de frontend-grootte. Daarom kan een enkel back-upexemplaar (zoals een back-up van een VM in Azure) overeenkomen met meerdere beveiligde instanties, afhankelijk van de frontend-grootte. [Meer informatie](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>RBAC (toegangs beheer op basis van rollen)

Raadpleeg de [RBAC-documentatie](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Herstel punt/herstel punt/bewaar punt/punt in tijd (PIT)

Een kopie van de oorspronkelijke gegevens waarvan een back-up wordt gemaakt. Een bewaar punt is gekoppeld aan een tijds tempel zodat u dit kunt gebruiken om een item te herstellen naar een bepaald punt in de tijd.

## <a name="recovery-services-vault"></a>Recovery Services-kluis

Een Azure Resource Manager resource van het type *micro soft. Recovery Services/kluizen*. Op dit moment worden Recovery Services kluizen gebruikt voor het maken van een back-up van de volgende werk belastingen: Azure-Vm's, SQL in azure Vm's, SAP HANA in azure-Vm's en Azure-bestands shares. Het wordt ook gebruikt om back-ups te maken van on-premises workloads met MARS, Azure Backup Server (MABS) en System Center DPM. [Meer informatie over Recovery Services-kluizen](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>Resourcegroep

Raadpleeg de [documentatie van Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group).

## <a name="rest-api"></a>REST-API

Raadpleeg de [documentatie van Azure rest API](https://docs.microsoft.com/rest/api/azure/).

## <a name="retention-rule"></a>Bewaar regel

Een door de gebruiker gedefinieerde regel die aangeeft hoe lang back-ups moeten worden bewaard.

## <a name="rpo-recovery-point-objective"></a>RPO (beoogd herstel punt)

RPO geeft het maximale gegevens verlies aan dat mogelijk is in een scenario met gegevens verlies. Dit wordt bepaald door de back-upfrequentie.

## <a name="rto-recovery-time-objective"></a>RTO (beoogde herstel tijd)

RTO geeft de maximale mogelijke tijd weer waarmee gegevens kunnen worden hersteld naar het laatst beschik bare tijdstip na een scenario voor gegevens verlies.

## <a name="scheduled-backup"></a>Geplande back-up

Een back-uptaak die automatisch wordt geactiveerd door het back-upbeleid dat voor het opgegeven item is geconfigureerd.

## <a name="secondary-region--paired-region"></a>Secundaire regio/gekoppelde regio

Een regionaal paar bestaat uit twee regio's binnen dezelfde geografie. De ene is de primaire regio en de andere is de secundaire regio. Gekoppelde regio's worden door sommige Azure-Services (inclusief Azure Backup met GRS-instellingen) gebruikt om bedrijfs continuïteit te garanderen en te beschermen tegen gegevens verlies. [Meer informatie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="soft-delete"></a>Voorlopig verwijderen

Zacht verwijderen is een functie die bescherming biedt tegen onbedoeld verwijderen van back-upgegevens. Met zacht verwijderen, zelfs als een schadelijke actor een back-up verwijdert (of als er per ongeluk back-upgegevens worden verwijderd), worden de back-upgegevens gedurende een extra periode bewaard, waardoor het herstel van het back-upitem zonder gegevens verlies wordt toegestaan. [Meer informatie](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>Momentopname

Een moment opname is een volledige, alleen-lezen kopie van een virtuele harde schijf (VHD) of een Azure-bestands share. Meer informatie over [moment opnamen van schijven](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk) en [moment opnamen van bestanden](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files).

## <a name="storage-account"></a>Storage-account

Raadpleeg de documentatie van het [opslag account](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="subscription"></a>Abonnement

Een Azure-abonnement is een logische container die wordt gebruikt voor het inrichten van resources in Azure. Het bevat de details van al uw resources, zoals virtuele machines (Vm's), data bases en meer.

## <a name="system-state-backup"></a>Systeem status back-up

(Werk belasting-specifieke termijn)

Maakt back-ups van bestanden van het besturings systeem. Met deze back-up kunt u herstellen wanneer een computer wordt opgestart, maar de systeem bestanden en het REGI ster gaan verloren. [Meer informatie](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>Tenant

Een tenant vertegenwoordigt een organisatie. Een tenant is een toegewezen Azure AD-exemplaar dat een organisatie of app-ontwikkelaar ontvangt wanneer deze een relatie start met Microsoft, door zich bijvoorbeeld aan te melden voor Azure, Microsoft Intune of Microsoft 365.

## <a name="unmanaged-disk"></a>Niet-beheerde schijf

Raadpleeg de [documentatie over niet-beheerde schijven](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance#azure-unmanaged-disks).

## <a name="vault"></a>Kluis

Een opslag entiteit in azure waarmee back-upgegevens worden opgeslagen. Het is ook een eenheid van RBAC en facturering. Er zijn momenteel twee typen kluizen: Recovery Services kluis en back-upkluis.

## <a name="vault-credentials"></a>Kluis referenties

Het kluis referentie bestand is een certificaat dat is gegenereerd door de portal voor elke kluis. Dit wordt gebruikt bij het registreren van een on-premises server bij de kluis. [Meer informatie](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>VNET (Virtual Network)

Raadpleeg de [VNET-documentatie](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows Volume Shadow Copy Service)

Raadpleeg de [VSS-documentatie](https://docs.microsoft.com/windows-server/storage/file-server/volume-shadow-copy-service).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Backup](backup-overview.md)
- [Architectuur en onderdelen van Azure Backup](backup-architecture.md)
