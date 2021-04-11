---
title: Azure Storage migratie handleiding
description: Overzicht van de hand leiding voor opslag migratie beschrijft de basis richtlijnen voor opslag migratie
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f6f00075c7c66679281d776f9472ec4a1a590d76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231473"
---
# <a name="azure-storage-migration-overview"></a>Overzicht van Azure Storage migratie

Dit artikel richt zich op opslag migraties naar Azure en biedt richt lijnen voor de volgende scenario's voor opslag migratie:

- Migratie van ongestructureerde gegevens, zoals bestanden en objecten
- Migratie van apparaten op basis van een blok, zoals schijven en Storage Area Networks (San's)

## <a name="migration-of-unstructured-data"></a>Migratie van ongestructureerde gegevens

Migratie van ongestructureerde gegevens omvat de volgende scenario's:

- Bestands migratie van NAS (Network Attached Storage) naar een van de Azure-bestands aanbiedingen:
  - [Azure Files](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [ISV-oplossingen (Independent Software Vendor)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).
- Object migratie van oplossingen voor object opslag naar het Azure object Storage-platform:
  - [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Migratie fasen

Een volledige migratie bestaat uit verschillende fasen: detectie, evaluatie en migratie.

![Diagram waarin de detectie-, evaluatie-en migratie fasen van de migratie worden weer gegeven](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Detectie fase

In de detectie fase bepaalt u alle bronnen die moeten worden gemigreerd zoals SMB-shares, NFS-export of object naam ruimten. U kunt deze fase hand matig uitvoeren of gebruikmaken van automatische hulpprogram ma's.

#### <a name="assessment-phase"></a>Beoordelings fase

De evaluatie fase is van cruciaal belang bij de uitleg van de beschik bare opties voor de migratie. Om het risico te verminderen tijdens de migratie en om veelvoorkomende Valk uilen te vermijden, volgt u deze drie stappen:

| Stappen voor evaluatie fase                     | Opties                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Kies een doel opslag service**            | -Azure Blob Storage en Data Lake Storage<br>-Azure Files<br>-Azure NetApp Files<br>-ISV-oplossingen |
| **Een migratie methode selecteren**                  | -Online<br>-Offline<br> -Combi natie van beide                                  |
| **Kies het beste migratie hulpprogramma voor de taak** | -Commerciële hulpprogram ma's (Azure en ISV)<br> -Open source                             


Er zijn verschillende commerciële hulpprogram ma's (ISV) die u kunnen helpen bij de evaluatie fase. Zie de [vergelijkings matrix](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

##### <a name="choose-a-target-storage-service"></a>Kies een doel opslag service

Het kiezen van een doel opslag service is afhankelijk van de toepassing of gebruikers die toegang hebben tot de gegevens. De juiste keuze is afhankelijk van de technische en financiële aspecten. Voer eerst een technische evaluatie uit om mogelijke doelen te beoordelen en te bepalen welke services voldoen aan de vereisten. Voer vervolgens een financiële beoordeling uit om de beste keuze te bepalen.

Evalueer de volgende aspecten van elke service om u te helpen bij het selecteren van de doel opslag service voor de migratie:

- Protocolondersteuning
- Prestatie kenmerken
- Limieten van de doel opslag service

Het volgende diagram is een vereenvoudigde beslissings structuur waarmee u de aanbevolen Azure File-Service kunt begeleiden. Als systeem eigen Azure-Services niet voldoen aan de vereisten, worden er diverse [ISV-oplossingen (Independent Software Vendor)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview) ondersteund.

Nadat u de technische evaluatie hebt voltooid en het juiste doel hebt geselecteerd, voert u een kosten beoordeling uit om de meest rendabele optie te bepalen.

![Basis beslissings structuur voor het kiezen van de juiste bestands service](./media/storage-migration-overview/files-decision-tree.png)

De limieten van de doel opslag service worden niet opgenomen in het diagram om de beslissings structuur eenvoudig te laten blijven. Zie de volgende onderwerpen voor meer informatie over de huidige limieten en om te bepalen of u uw keuzes moet aanpassen op basis van deze.

- [Limieten van opslag account](/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
- [Blob Storage limieten](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-blob-storage-limits)
- [Schaalbaarheids- en prestatiedoelen in Azure Files](/azure/storage/files/storage-files-scale-targets)
- [Azure NetApp Files resource limieten](/azure/azure-netapp-files/azure-netapp-files-resource-limits)

Als een van de limieten een blok kering vormt voor het gebruik van een service, ondersteunt Azure diverse opslag leveranciers die hun oplossingen aanbieden op Azure Marketplace. Zie [Azure Storage partners voor primaire en secundaire opslag](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview)voor meer informatie over de gevalideerde ISV-partners die bestands services aanbieden.

##### <a name="select-the-migration-method"></a>De migratie methode selecteren

Er zijn twee basis migratie methoden voor opslag migraties.

- **Online**. De online methode maakt gebruik van het netwerk voor gegevens migratie. Het open bare Internet of [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) kan worden gebruikt. Als de service geen openbaar eind punt heeft, moet u een VPN met een openbaar Internet gebruiken.
- **Breken.** De offline methode maakt gebruik van een van de [Azure data Box](https://azure.microsoft.com/services/databox/) apparaten.

De beslissing om een online methode te gebruiken in plaats van een offline methode is afhankelijk van de beschik bare netwerk bandbreedte. De online methode verdient de voor keur in gevallen waarin voldoende netwerk bandbreedte beschikbaar is om een migratie binnen de benodigde tijd lijn uit te voeren.

Het is mogelijk om een combi natie van beide methoden te gebruiken, offline methode voor de eerste bulk migratie en een online methode voor incrementele migratie van wijzigingen. Het gebruik van beide methoden vereist een hoog niveau van coördinatie en wordt daarom niet aanbevolen. Als u beide methoden wilt gebruiken, worden de gegevens sets die online worden gemigreerd, geïsoleerd van de gegevens sets die offline zijn gemigreerd.

Zie [een Azure-oplossing voor gegevens overdracht kiezen](/azure/storage/common/storage-choose-data-transfer-solution) en [migreren naar Azure-bestands shares](/azure/storage/files/storage-files-migration-overview)voor meer informatie over de verschillende migratie methoden en-richt lijnen.

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Kies het beste migratie hulpprogramma voor de taak

Er zijn verschillende migratie hulpprogramma's die u kunt gebruiken om de migratie uit te voeren. Sommige zijn open source, zoals AzCopy, Robocopy, Xcopy en rsync, terwijl anderen commercieel zijn. Een lijst met beschik bare commerciële hulp middelen en vergelijking tussen deze hulpprogram ma's is beschikbaar in onze [vergelijkings matrix](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

Open source-hulpprogram ma's zijn goed geschikt voor kleinschalige migraties. Voor migratie van Windows-bestands servers naar Azure Files, raadt micro soft aan om te beginnen met Azure Files systeem eigen mogelijkheid en [Azure file sync](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-file-sync)te gebruiken. Voor complexere migraties die bestaan uit verschillende bronnen, grote capaciteit of speciale vereisten, zoals het beperken of gedetailleerd rapporteren met controle mogelijkheden, zijn commerciële hulp middelen de beste keuze. Deze hulpprogram ma's maken de migratie eenvoudiger en verminderen het risico aanzienlijk. De meeste commerciële hulpprogram ma's kunnen ook de detectie uitvoeren, die een waardevolle invoer voor de evaluatie biedt.

#### <a name="migration-phase"></a>Migratie fase

De migratie fase is de laatste migratie stap waarbij gegevens worden verplaatst en gemigreerd. Normaal gesp roken voert u de migratie fase meerdere keren uit om een eenvoudigere schakeling uit te voeren. De migratie fase bestaat uit de volgende stappen:

1. **Initiële migratie.** Met de eerste migratie stap worden alle gegevens van de bron naar het doel gemigreerd. Met deze stap wordt het meren deel van de gegevens die moeten worden gemigreerd, gemigreerd.
2. **Synchronisatie.** Bij een hersynchronisatie-bewerking worden alle gegevens gemigreerd die zijn gewijzigd na de eerste stap in de migratie. U kunt deze stap verschillende keren herhalen als er talrijke wijzigingen zijn. Het doel van het uitvoeren van meerdere Resync bewerkingen is het verminderen van de tijd die nodig is voor de laatste stap. Voor inactieve gegevens en voor gegevens die geen wijzigingen hebben (zoals back-up-of Archief gegevens), kunt u deze stap overs Laan.
3. **Definitieve overgang**. Met de laatste stap voor overschakelen wordt het actieve gebruik van de gegevens van de bron naar het doel gewisseld en wordt de bron buiten gebruik gesteld.

De duur van de migratie voor ongestructureerde gegevens is afhankelijk van verschillende aspecten. Buiten de gekozen methode zijn de meest kritieke factoren de totale grootte van de distributie van gegevens en bestands grootte. Hoe groter de totale gegevensset, hoe langer de migratie tijd. Hoe kleiner de gemiddelde bestands grootte, hoe langer de migratie tijd. Als u een groot aantal kleine bestanden hebt, kunt u deze archiveren in grotere bestanden (zoals een. tar-of. zip-bestand), indien van toepassing, om de totale migratie tijd te verminderen.

## <a name="migration-of-block-based-devices"></a>Migratie van apparaten op basis van een blok

Migratie van apparaten op basis van een blok wordt doorgaans uitgevoerd als onderdeel van de migratie van virtuele machines of fysieke hosts. Het is een gemeen schappelijke, veelvoorkomende oplossing om de blok opslag te vertragen tot na de migratie. Als u deze beslissingen voorbereidt op basis van de juiste overwegingen voor de werkbelasting vereisten, leidt dit tot een soepelere migratie naar de Cloud.

Als u werk belastingen wilt verkennen om te migreren en te volgen, raadpleegt u de [Azure Disk Storage documentatie](/azure/virtual-machines/disks-types)en resources op de [product pagina van Disk Storage](https://azure.microsoft.com/services/storage/disks/#resources). U kunt meer informatie over welke schijven aan uw vereisten voldoen en de nieuwste mogelijkheden, zoals [schijf bursting](/azure/virtual-machines/disk-bursting). Zie de [Azure migrate](/azure/migrate/) -documentatie voor informatie over het migreren van de virtuele machines met de onderliggende op het apparaat gebaseerde apparaten.

## <a name="see-also"></a>Zie ook

- [Een Azure-oplossing voor gegevensoverdracht kiezen](/azure/storage/common/storage-choose-data-transfer-solution)
- [Vergelijking van hulpprogram ma's voor commerciële migratie](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Migreren naar Azure-bestandsshares](/azure/storage/files/storage-files-migration-overview)
- [Migreren naar Data Lake Storage met WANdisco LiveData platform voor Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Gegevens kopiëren of verplaatsen naar Azure Storage met AzCopy](https://aka.ms/azcopy)
- [Grote gegevens sets migreren naar Azure Blob Storage met AzReplicate](https://github.com/Azure/AzReplicate/tree/master/)