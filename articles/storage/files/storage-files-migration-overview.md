---
title: Migreren naar Azure-bestandsshares
description: Meer informatie over migraties naar Azure-bestands shares en uw migratiehandleiding zoeken.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a6335d90625f860984ccbfd224955a97a32b731f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785213"
---
# <a name="migrate-to-azure-file-shares"></a>Migreren naar Azure-bestandsshares

In dit artikel worden de basisaspecten van een migratie naar Azure-bestands shares beschreven.

Dit artikel bevat de basisprincipes van migratie en een tabel met migratiehandleidingen. Deze handleidingen helpen u bij het verplaatsen van uw bestanden naar Azure-bestands shares. De handleidingen zijn ingedeeld op basis van waar uw gegevens zich en naar welk implementatiemodel (alleen-cloud of hybride) u verplaatst.

## <a name="migration-basics"></a>Basisbeginselen van migratie

Azure heeft meerdere beschikbare typen cloudopslag. Een fundamenteel aspect van bestandsmigraties naar Azure is bepalen welke Azure-opslagoptie het meest voor uw gegevens is.

[Azure-bestands](storage-files-introduction.md) shares zijn geschikt voor algemene bestandsgegevens. Deze gegevens omvatten alles waar u een on-premises SMB- of NFS-share voor gebruikt. Met [Azure File Sync](../file-sync/file-sync-planning.md)kunt u de inhoud van verschillende Azure-bestands shares in de cache opslaan op servers waarop Windows Server on-premises wordt uitgevoerd.

Voor een app die momenteel wordt uitgevoerd op een on-premises server, kan het opslaan van bestanden in een Azure-bestands share een goede keuze zijn. U kunt de app verplaatsen naar Azure en Azure-bestands shares gebruiken als gedeelde opslag. Voor dit scenario [kunt u ook Azure Disks](../../virtual-machines/managed-disks-overview.md) overwegen.

Sommige cloud-apps zijn niet afhankelijk van SMB of van toegang tot gegevens op de computer of gedeelde toegang. Voor deze apps is objectopslag zoals [Azure-blobs](../blobs/storage-blobs-overview.md) vaak de beste keuze.

De sleutel bij elke migratie is het vastleggen van alle van toepassing zijnde bestandstrouwheid bij het verplaatsen van uw bestanden van hun huidige opslaglocatie naar Azure. Hoeveel betrouwbaarheid de Azure-opslagoptie ondersteunt en hoeveel uw scenario vereist, helpt u ook bij het kiezen van de juiste Azure-opslag. Bestandsgegevens voor algemeen gebruik zijn traditioneel afhankelijk van bestandsmetagegevens. App-gegevens zijn dat mogelijk niet.

Dit zijn de twee basisonderdelen van een bestand:

- **Gegevensstroom:** in de gegevensstroom van een bestand wordt de bestandsinhoud opgeslagen.
- **Bestandsmetagegevens:** de metagegevens van het bestand bevatten de volgende subonderdelen:
   * Bestandskenmerken zoals alleen-lezen
   * Bestandsmachtigingen, die kunnen worden aangeduid als *NTFS-machtigingen* of ACL's voor *bestanden en mappen*
   * Tijdstempels, met name het maken en de laatste gewijzigde tijdstempels
   * Een alternatieve gegevensstroom, een ruimte voor het opslaan van grotere hoeveelheden niet-standaardeigenschappen

Bestandstrouwheid in een migratie kan worden gedefinieerd als de mogelijkheid om:

- Sla alle toepasselijke bestandsgegevens op de bron op.
- Bestanden overdragen met het hulpprogramma voor migratie.
- Sla bestanden op in de doelopslag van de migratie.

Om ervoor te zorgen dat uw migratie soepel verloopt, identificeert u het [beste](#migration-toolbox) kopieerprogramma voor uw behoeften en koppelt u een opslagdoel aan uw bron.

Rekening houdend met de vorige informatie, kunt u zien dat de doelopslag voor bestanden voor algemeen gebruik in Azure [Azure-bestands shares is.](storage-files-introduction.md)

In tegenstelling tot objectopslag in Azure-blobs kan een Azure-bestands share systeemeigen bestandsmetagegevens opslaan. Azure-bestands shares behouden ook de bestands- en maphiërarchie, kenmerken en machtigingen. NTFS-machtigingen kunnen worden opgeslagen in bestanden en mappen omdat ze on-premises zijn.

Een gebruiker van Active Directory, de on-premises domeincontroller, heeft standaard toegang tot een Azure-bestands share. Zo kan een gebruiker van Azure Active Directory Domain Services (Azure AD DS). Elk gebruikt zijn huidige identiteit om toegang te krijgen op basis van machtigingen voor delen en ACL's voor bestanden en mappen. Dit gedrag is vergelijkbaar met een gebruiker die verbinding maakt met een on-premises bestands share.

De alternatieve gegevensstroom is het primaire aspect van bestandstrouwheid dat momenteel niet kan worden opgeslagen in een bestand in een Azure-bestands share. Deze blijft on-premises behouden wanneer Azure File Sync wordt gebruikt.

Meer informatie over [Azure AD-verificatie](storage-files-identity-auth-active-directory-enable.md) [en Azure AD DS voor](storage-files-identity-auth-active-directory-domain-service-enable.md) Azure-bestands shares.

## <a name="migration-guides"></a>Migratiehandleidingen

De volgende tabel bevat gedetailleerde migratiehandleidingen.

De tabel gebruiken:

1. Zoek de rij voor het bronsysteem waar uw bestanden momenteel op zijn opgeslagen.

1. Kies een van deze doelen:

   - Een hybride implementatie die gebruik Azure File Sync om de inhoud van Azure-bestands shares on-premises in de cache op te nemen
   - Azure-bestands shares in de cloud

   Selecteer de doelkolom die overeenkomt met uw keuze.

1. Op het snijpunt van de bron en het doel vindt u een tabelcel met beschikbare migratiescenario's. Selecteer er een om rechtstreeks een koppeling naar de gedetailleerde migratiehandleiding te maken.

Een scenario zonder een koppeling heeft nog geen gepubliceerde migratiehandleiding. Controleer deze tabel af en toe op updates. Nieuwe handleidingen worden gepubliceerd wanneer ze beschikbaar zijn.

| Bron | Doel: </br>Hybride implementatie | Doel: </br>Cloudimplementatie |
|:---|:--|:--|
| | Combinatie van hulpprogramma's:| Combinatie van hulpprogramma's: |
| Windows Server 2012 R2 en hoger | <ul><li>[Azure File Sync](../file-sync/file-sync-deployment-guide.md)</li><li>[Azure File Sync en Azure DataBox](../file-sync/file-sync-offline-data-transfer.md)</li></ul> | <ul><li>Via RoboCopy naar een gemonteerde Azure-bestands share</li><li>Via Azure File Sync</li></ul> |
| Windows Server 2012 en eerder | <ul><li>Via DataBox en Azure File Sync recente server os</li><li>Via Storage Migration Service naar recente server met Azure File Sync en vervolgens uploaden</li></ul> | <ul><li>Via Storage Migration Service naar recente server met Azure File Sync</li><li>Via RoboCopy naar een gemonteerde Azure-bestands share</li></ul> |
| NaS (Network-Attached Storage) | <ul><li>[Via Azure File Sync uploaden](storage-files-migration-nas-hybrid.md)</li><li>[Via DataBox + Azure File Sync](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[Via DataBox](storage-files-migration-nas-cloud-databox.md)</li><li>Via RoboCopy naar een gemonteerde Azure-bestands share</li></ul> |
| Linux/Samba | <ul><li>[Azure File Sync en RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Via RoboCopy naar een gemonteerde Azure-bestands share</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 of StorSimple Cloud Appliance 8600 | <ul><li>[Via toegewezen cloudservice voor gegevensmigratie](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Via Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Migratie-werkset

### <a name="file-copy-tools"></a>Hulpprogramma's voor het kopiëren van bestanden

Er zijn verschillende hulpprogramma's voor het kopiëren van bestanden beschikbaar van Microsoft en andere hulpprogramma's. Als u het juiste hulpprogramma voor uw migratiescenario wilt selecteren, moet u rekening houden met de volgende fundamentele vragen:

* Ondersteunt het hulpprogramma de bron- en doellocaties voor het kopiëren van bestanden?

* Ondersteunt het hulpprogramma uw netwerkpad of beschikbare protocollen (zoals REST, SMB of NFS) tussen de bron- en doelopslaglocaties?

* Behoudt het hulpprogramma de benodigde bestandstrouw die wordt ondersteund door uw bron- en doellocaties?

    In sommige gevallen biedt uw doelopslag geen ondersteuning voor dezelfde betrouwbaarheid als uw bron. Als de doelopslag voldoende is voor uw behoeften, moet het hulpprogramma alleen overeenkomen met de bestandstrouwe mogelijkheden van het doel.

* Bevat het hulpprogramma functies waarmee het in uw migratiestrategie past?

    Bedenk bijvoorbeeld of u met het hulpprogramma uw downtime kunt minimaliseren.
    
    Wanneer een hulpprogramma een optie voor het spiegelen van een bron naar een doel ondersteunt, kunt u deze vaak meerdere keren uitvoeren op dezelfde bron en hetzelfde doel terwijl de bron toegankelijk blijft.

    De eerste keer dat u het hulpprogramma hebt uitgevoerd, wordt het grootste deel van de gegevens gekopieerd. Deze eerste run kan even duren. Het duurt vaak langer dan u wilt om de gegevensbron offline te halen voor uw bedrijfsprocessen.

    Door een bron te spiegelen naar een doel (zoals bij **Robocopy /INSTRUMENT),** kunt u het hulpprogramma opnieuw uitvoeren op dezelfde bron en hetzelfde doel. De run is veel sneller omdat deze alleen bronwijzigingen hoeft te transporteren die na de vorige run optreden. Als u een hulpprogramma voor kopiëren op deze manier opnieuw wilt gebruiken, kan de downtime aanzienlijk worden verminderd.

De volgende tabel classificeert Microsoft-hulpprogramma's en hun huidige geschiktheid voor Azure-bestands shares:

| Aanbevolen | Hulpprogramma | Ondersteuning voor Azure-bestands shares | Behoud van bestandstrouw |
| :-: | :-- | :---- | :---- |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Ondersteund. Azure-bestands shares kunnen worden aangesloten als netwerkstations. | Volledige betrouwbaarheid.* |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Systeemeigen geïntegreerd in Azure-bestands shares. | Volledige betrouwbaarheid.* |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Storage Migration Service | Indirect ondersteund. Azure-bestands shares kunnen worden aangesloten als netwerkstations op sms-doelservers. | Volledige betrouwbaarheid.* |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>versie 10.6 | Ondersteund. | Biedt geen ondersteuning voor het kopiëren van de hoofd-ACL van de bron, anders volledige betrouwbaarheid.* </br>[Meer informatie over het gebruik van AzCopy met Azure-bestands shares](../common/storage-use-azcopy-files.md) |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Ondersteund. | DataBox biedt volledige ondersteuning voor metagegevens. |
|![Niet volledig aanbevolen](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer </br>versie 1.14 | Ondersteund. | Kopieert geen ACL's. Ondersteunt tijdstempels.  |
|![Niet aanbevolen](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Ondersteund. | Kopieert geen metagegevens. |
|||||

*\* Volledige betrouwbaarheid: voldoet aan of overschrijdt de mogelijkheden van Azure-bestands delen.*

### <a name="migration-helper-tools"></a>Hulphulpprogramma's voor migratie

In deze sectie worden hulpprogramma's beschreven waarmee u migraties kunt plannen en uitvoeren.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy van Microsoft Corporation

RoboCopy is een van de hulpprogramma's die het meest van toepassing zijn op bestandsmigraties. Deze wordt geleverd als onderdeel van Windows. De belangrijkste [RoboCopy-documentatie](/windows-server/administration/windows-commands/robocopy) is een nuttige resource voor de vele opties van dit hulpprogramma.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize van JAM Software GmbH

Azure File Sync schaalt voornamelijk met het aantal items (bestanden en mappen) en niet met de totale hoeveelheid opslagruimte. Met het hulpprogramma TreeSize kunt u het aantal items op uw Windows Server-volumes bepalen.

U kunt het hulpprogramma gebruiken om een perspectief te maken voordat een Azure File Sync [geïmplementeerd.](../file-sync/file-sync-deployment-guide.md) U kunt deze ook gebruiken wanneer opslag in cloudlagen wordt ingeschakeld na de implementatie. In dat scenario ziet u het aantal items en welke directories het meest gebruikmaken van uw servercache.

De geteste versie van het hulpprogramma is versie 4.4.1. Het is compatibel met bestanden in cloudlagen. Het hulpprogramma zorgt niet voor het terughalen van gelaagde bestanden tijdens de normale werking.

## <a name="next-steps"></a>Volgende stappen

1. Maak een plan voor de implementatie van Azure-bestands shares (alleen-cloud of hybride) die u wilt.
1. Bekijk de lijst met beschikbare migratiehandleidingen voor de gedetailleerde handleiding die overeenkomt met uw bron en implementatie van Azure-bestands shares.

Meer informatie over de Azure Files technologieën die in dit artikel worden genoemd:

* [Overzicht van Azure-bestands delen](storage-files-introduction.md)
* [Planning voor een Azure Files Sync-implementatie](../file-sync/file-sync-planning.md)
* [Azure File Sync: Opslag in cloudlagen](../file-sync/file-sync-cloud-tiering-overview.md)