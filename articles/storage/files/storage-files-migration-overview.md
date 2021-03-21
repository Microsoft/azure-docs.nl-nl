---
title: Migreren naar Azure-bestandsshares
description: Meer informatie over migraties naar Azure-bestands shares en de migratie handleiding.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 27056f39885949d52c9fcc0d1472033cfc8f9aa0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554867"
---
# <a name="migrate-to-azure-file-shares"></a>Migreren naar Azure-bestandsshares

In dit artikel worden de basis aspecten beschreven van een migratie naar Azure-bestands shares.

Dit artikel bevat basis informatie over de migratie en een tabel met migratie handleidingen. Met deze hand leidingen kunt u uw bestanden naar Azure-bestands shares verplaatsen. De hulp lijnen zijn ingedeeld op basis van de locatie van uw gegevens en van het implementatie model (alleen Cloud of hybride) waarnaar u wilt verplaatsen.

## <a name="migration-basics"></a>Basis beginselen migratie

Azure heeft meerdere beschik bare typen Cloud opslag. Een fundamenteel aspect van bestands migratie naar Azure is bepalen welke Azure-opslag optie geschikt is voor uw gegevens.

[Azure-bestands shares](storage-files-introduction.md) zijn geschikt voor bestands gegevens voor algemene doel einden. Deze gegevens bevatten alles wat u gebruikt voor een on-premises SMB-of NFS-share voor. Met [Azure file sync](storage-sync-files-planning.md)kunt u de inhoud van verschillende Azure-bestands shares in de cache opslaan op servers waarop Windows Server on-premises wordt uitgevoerd.

Voor een app die momenteel op een on-premises server wordt uitgevoerd, kan het opslaan van bestanden in een Azure-bestands share een goede keuze zijn. U kunt de app naar Azure verplaatsen en Azure-bestands shares als gedeelde opslag gebruiken. U kunt ook rekening houden met [Azure-schijven](../../virtual-machines/managed-disks-overview.md) voor dit scenario.

Sommige Cloud-apps zijn niet afhankelijk van SMB of op machine-lokale gegevens toegang of gedeelde toegang. Voor die apps is object opslag, zoals [Azure-blobs](../blobs/storage-blobs-overview.md) , vaak de beste keuze.

De sleutel in een migratie is het vastleggen van alle toepasselijke bestands kwaliteit bij het verplaatsen van de bestanden van de huidige opslag locatie naar Azure. De betrouw baarheid van de Azure-opslag optie ondersteunt en de hoeveelheid die uw scenario nodig heeft, helpt u ook bij het kiezen van de juiste Azure-opslag. Bestands gegevens voor algemeen gebruik zijn van oudsher afhankelijk van meta gegevens van bestanden. App-gegevens zijn mogelijk niet.

Dit zijn de twee basis onderdelen van een bestand:

- **Gegevens stroom**: de gegevens stroom van een bestand slaat de bestands inhoud op.
- **Meta gegevens van bestand**: de meta gegevens van het bestand hebben de volgende subonderdelen:
   * Bestands kenmerken zoals alleen-lezen
   * Bestands machtigingen, waarnaar kan worden verwezen als *NTFS-machtigingen* of *acl's voor bestanden en mappen*
   * Tijds tempels, met name het maken en de laatste gewijzigde tijds tempels
   * Een alternatieve gegevens stroom, een ruimte voor het opslaan van grotere hoeveel heden niet-standaard eigenschappen

Bestands kwaliteit in een migratie kan worden gedefinieerd als de mogelijkheid om:

- Sla alle toepasselijke bestands gegevens op de bron op.
- Bestanden overdragen met het hulp programma voor migratie.
- Sla de bestanden op in de doel opslag van de migratie.

Om ervoor te zorgen dat uw migratie probleemloos verloopt, identificeert u [het beste Kopieer programma voor uw behoeften](#migration-toolbox) en past u een opslag doel aan uw bron aan.

Met de voor gaande informatie kunt u zien dat de doel opslag voor algemene bestanden in azure [Azure-bestands shares](storage-files-introduction.md)is.

In tegens telling tot object opslag in azure blobs, kan een Azure-bestands share systeem eigen meta gegevens van een bestand opslaan. Azure-bestands shares behouden ook de bestands-en maphiërarchie, kenmerken en machtigingen. NTFS-machtigingen kunnen worden opgeslagen in bestanden en mappen omdat ze on-premises zijn.

Een gebruiker van Active Directory, die de on-premises domein controller is, kan systeem eigen toegang krijgen tot een Azure-bestands share. Dit kan een gebruiker van Azure Active Directory Domain Services (Azure AD DS) zijn. Elk gebruikt hun huidige identiteit om toegang te krijgen op basis van share machtigingen en de Acl's voor bestanden en mappen. Dit gedrag is vergelijkbaar met een gebruiker die verbinding maakt met een on-premises bestands share.

De alternatieve gegevens stroom is het belangrijkste aspect van bestands kwaliteit dat momenteel niet kan worden opgeslagen in een bestand in een Azure-bestands share. De on-premises blijven behouden wanneer Azure File Sync wordt gebruikt.

Meer informatie over [Azure AD-verificatie](storage-files-identity-auth-active-directory-enable.md) en [Azure AD DS-verificatie](storage-files-identity-auth-active-directory-domain-service-enable.md) voor Azure-bestands shares.

## <a name="migration-guides"></a>Migratiehandleidingen

De volgende tabel bevat gedetailleerde migratie handleidingen.

De tabel gebruiken:

1. Zoek de rij voor het bron systeem waarop uw bestanden momenteel zijn opgeslagen.

1. Kies een van de volgende doelen:

   - Een hybride implementatie met Azure File Sync om de inhoud van Azure-bestands shares on-premises op te slaan in de cache
   - Azure-bestands shares in de Cloud

   Selecteer de doel kolom die overeenkomt met uw keuze.

1. Binnen het snij punt van de bron en het doel worden in een tabelcel beschik bare migratie scenario's weer gegeven. Selecteer een om rechtstreeks te koppelen aan de gedetailleerde migratie handleiding.

Een scenario zonder een koppeling heeft nog geen gepubliceerde migratie handleiding. Controleer deze tabel af en toe voor updates. Nieuwe gidsen worden gepubliceerd wanneer ze beschikbaar zijn.

| Bron | Doel: </br>Hybride implementatie | Doel: </br>Implementatie in de Cloud |
|:---|:--|:--|
| | Combi natie van gereedschap:| Combi natie van gereedschap: |
| Windows Server 2012 R2 en hoger | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync en Azure DataBox](storage-sync-offline-data-transfer.md)</li></ul> | <ul><li>Via RoboCopy naar een gekoppelde Azure-bestands share</li><li>Via Azure File Sync</li></ul> |
| Windows Server 2012 en lager | <ul><li>Via DataBox en Azure File Sync naar recent server-besturings systeem</li><li>Via de opslag migratie service naar de recente server met Azure File Sync en upload</li></ul> | <ul><li>Via Storage Migration service naar recente server met Azure File Sync</li><li>Via RoboCopy naar een gekoppelde Azure-bestands share</li></ul> |
| NAS (Network-Attached Storage) | <ul><li>[Via Azure File Sync uploaden](storage-files-migration-nas-hybrid.md)</li><li>[Via DataBox + Azure File Sync](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>Via RoboCopy naar een gekoppelde Azure-bestands share</li></ul> |
| Linux-Samba | <ul><li>[Azure File Sync en RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Via RoboCopy naar een gekoppelde Azure-bestands share</li></ul> |
| Microsoft Azure StorSimple Cloud apparaat 8100 of StorSimple Cloud Appliance 8600 | <ul><li>[Via een exclusieve gegevens migratie Cloud service](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Via Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Werkset voor migratie

### <a name="file-copy-tools"></a>Hulpprogram ma's voor het kopiëren van bestanden

Er zijn verschillende hulpprogram ma's voor het kopiëren van bestanden beschikbaar van micro soft en anderen. Als u het juiste hulp programma voor uw migratie scenario wilt selecteren, moet u rekening houden met de volgende belang rijke vragen:

* Ondersteunt het hulp programma de bron-en doel locaties voor het kopiëren van bestanden?

* Ondersteunt het hulp programma het netwerkpad of beschik bare protocollen (zoals REST, SMB of NFS) tussen de bron-en doel opslag locaties?

* Behoudt het hulp programma de benodigde bestands kwaliteit die wordt ondersteund door uw bron-en doel locaties?

    In sommige gevallen ondersteunt uw doel opslag niet dezelfde betrouw baarheid als uw bron. Als de doel opslag voldoende is voor uw behoeften, moet het hulp programma alleen overeenkomen met de bestands betrouw baarheid van het doel.

* Heeft het hulp programma functies die ervoor zorgen dat ze in uw migratie strategie passen?

    U kunt bijvoorbeeld overwegen of u met het hulp programma uw downtime kunt minimaliseren.
    
    Wanneer een hulp programma een optie ondersteunt om een bron naar een doel te spie gelen, kunt u deze vaak meerdere keren op dezelfde bron en hetzelfde doel uitvoeren terwijl de bron toegankelijk blijft.

    De eerste keer dat u het hulp programma uitvoert, wordt het meren deel van de gegevens gekopieerd. Deze eerste uitvoering kan enige tijd duren. Het duurt vaak langer dan u wilt voor het offline halen van de gegevens bron voor uw bedrijfs processen.

    Door een bron te spie gelen naar een doel (net als bij **Robocopy/Mir**), kunt u het hulp programma opnieuw uitvoeren op dezelfde bron en hetzelfde doel. De uitvoering is veel sneller omdat er alleen bron wijzigingen moeten worden getransporteerd die optreden na de vorige uitvoering. Het opnieuw uitvoeren van een kopieer programma op deze manier kan uitval tijd aanzienlijk verminderen.

De volgende tabel classificeert micro soft-hulpprogram ma's en hun huidige geschiktheid voor Azure-bestands shares:

| Aanbevolen | Hulpprogramma | Ondersteuning voor Azure-bestands shares | Behoud van bestands kwaliteit |
| :-: | :-- | :---- | :---- |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Ondersteund. Azure-bestands shares kunnen worden gekoppeld als netwerk stations. | Volledige kwaliteit. * |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Systeem eigen geïntegreerd in azure-bestands shares. | Volledige kwaliteit. * |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Opslag migratie service | Indirect ondersteund. Azure-bestands shares kunnen worden gekoppeld als netwerk stations op SMS-doel servers. | Volledige kwaliteit. * |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>versie 10,6 | Ondersteund. | Ondersteunt geen kopie van de toegangs beheer lijst van de bron-root, anders volledige kwaliteit. * </br>[Meer informatie over het gebruik van AzCopy met Azure-bestands shares](../common/storage-use-azcopy-files.md) |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Ondersteund. | DataBox biedt volledige ondersteuning voor meta gegevens. |
|![Niet volledig aanbevolen](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer </br>versie 1,14 | Ondersteund. | Er worden geen Acl's gekopieerd. Ondersteunt tijds tempels.  |
|![Niet aanbevolen](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Ondersteund. | Meta gegevens worden niet gekopieerd. |
|||||

*\* Volledige kwaliteit: voldoet aan of overschrijdt de mogelijkheden van Azure file-share.*

### <a name="migration-helper-tools"></a>Hulpprogram ma's voor migratie hulp

In deze sectie worden hulpprogram ma's beschreven die u helpen bij het plannen en uitvoeren van migraties.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy van micro soft Corporation

RoboCopy is een van de hulpprogram ma's die het meest van toepassing zijn op bestands migraties. Het wordt geleverd als onderdeel van Windows. De belangrijkste [Robocopy-documentatie](/windows-server/administration/windows-commands/robocopy) is een handige resource voor de vele opties van dit hulp programma.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize van een VASTGELOPEN software GmbH

Azure File Sync schaalt voornamelijk met het aantal items (bestanden en mappen) en niet met de totale opslag hoeveelheid. Met het hulp programma TreeSize kunt u het aantal items op uw Windows Server-volumes bepalen.

U kunt het hulp programma gebruiken om een perspectief te maken voor een [Azure file sync-implementatie](storage-sync-files-deployment-guide.md). U kunt deze ook gebruiken wanneer Cloud lagen worden gebruikt na de implementatie. In dat scenario ziet u het aantal items en welke directory's uw server cache het meest gebruiken.

De geteste versie van het hulp programma is versie 4.4.1. Het is compatibel met Cloud-gelaagde bestanden. Het hulp programma veroorzaakt geen gelaagde bestanden tijdens de normale werking.

## <a name="next-steps"></a>Volgende stappen

1. Maak een plan voor de implementatie van Azure-bestands shares (alleen in de Cloud of hybride) die u wilt.
1. Bekijk de lijst met beschik bare migratie handleidingen om de gedetailleerde hand leiding te vinden die overeenkomt met uw bron en de implementatie van Azure-bestands shares.

Meer informatie over de Azure Files technologieën die in dit artikel worden genoemd:

* [Overzicht van de Azure-bestands share](storage-files-introduction.md)
* [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
* [Azure File Sync: Cloud lagen](storage-sync-cloud-tiering-overview.md)