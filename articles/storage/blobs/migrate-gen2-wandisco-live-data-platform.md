---
title: Data Lake Storage-en WANdisco LiveData-platform voor Azure (preview-versie)
description: On-premises Hadoop-gegevens migreren naar Azure Data Lake Storage Gen2 met behulp van het WANdisco LiveData-platform voor Azure.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/06/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 9da6ea7abf57ffecc900a6dbef065a8c6b123e61
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810965"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Voldoen aan veeleisende migratie vereisten met het WANdisco LiveData platform voor Azure (preview)

On-premises Hadoop-gegevens migreren naar Azure Data Lake Storage Gen2 met behulp van het [WANdisco LiveData-platform voor Azure](https://docs.wandisco.com/live-data-platform/docs/landing/). Dit platform elimineert de nood zaak van uitval tijd van toepassingen, verwijdert de kans op gegevens verlies en zorgt voor consistentie van gegevens, zelfs wanneer bewerkingen on-premises worden voortgezet.  

> [!NOTE]
> WANdisco LiveData platform voor Azure is in open bare preview. Zie [ondersteunde regio's](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions)voor regionale Beschik baarheid.

Het platform bestaat uit twee services: [LiveData Migrator voor Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) om actieve gebruikte gegevens van on-premises omgevingen te migreren naar Azure Storage en het [LiveData-vlak voor Azure](https://www.wandisco.com/products/livedata-plane-for-azure) , dat ervoor zorgt dat alle gewijzigde gegevens of opname gegevens consistent worden gerepliceerd. 

> [!div class="mx-imgBorder"]
> ![Afbeelding van overzicht van live data platform](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

U kunt beide services beheren door gebruik te maken van de Azure Portal en de Azure CLI en beide hetzelfde facturerings model voor betalen per gebruik als alle andere Azure-Services volgen. Het LiveData-platform voor Azure-verbruik wordt weer gegeven op dezelfde maandelijkse Azure-factuur en biedt een consistente en handige manier om uw gebruik bij te houden en te controleren.

In tegens telling tot het _offline_ migreren van gegevens door [statische gegevens te kopiëren naar Azure data Box](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster), of door gebruik te maken van Hadoop-hulpprogram ma's zoals [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), kunt u de volledige werking van uw bedrijfs systemen behouden tijdens _online_ migratie met WANdisco LiveData voor Azure. Zorg ervoor dat uw big data omgevingen werken, zelfs wanneer ze hun gegevens verplaatsen naar Azure.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Belangrijkste functies van het WANdisco LiveData platform voor Azure

[WANdisco LiveData platform voor Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) maakt gebruik van een unieke, op Wide Area-netwerk geschikte consensus-Engine om consistentie van gegevens te krijgen en om gegevens replicatie op schaal uit te voeren terwijl toepassingen de gegevens onder replicatie kunnen blijven wijzigen.  

De belangrijkste functies van het platform zijn onder andere:

- **Gegevens consistentie**: Los de uitdagingen op voor het migreren van grote gegevens volumes tussen omgevingen en het behouden van de gegevens consistent tussen de doorvoer migratie van opslag systemen, zelfs als ze zich onder een voortdurende wijziging bevinden. Gebruik de unieke, Wide-Area netwerk ondersteuning van WANdisco direct in azure om consistentie van gegevens te krijgen en gegevens te migreren met consistentie garanties tijdens de gegevens wijzigingen.

- **Bewerkingen onderhouden**: omdat toepassingen gegevens kunnen blijven maken, wijzigen, lezen en verwijderen tijdens de migratie, is het niet nodig om zakelijke bewerkingen te onderbreken of een onderbrekings venster te introduceren om Big data naar Azure te migreren. Ga verder met het uitvoeren van toepassingen, de analyse-infra structuur, opname taken en andere verwerking.

- **Resultaten valideren**: end-to-end validatie dat uw gegevens effectief kunnen worden gebruikt wanneer deze naar Azure worden gemigreerd, moet u de werk belasting van productie toepassingen tegen hen uitvoeren. Alleen een LiveData-service biedt dit zonder het risico op gegevens divergentie te introduceren door de consistentie van gegevens te hand haven, ongeacht of de wijziging plaatsvindt bij de bron of het doel van uw migratie. Testen en valideren van het gedrag van de toepassing zonder Risico's of wijziging van uw processen en systemen.

- **Minder complexiteit**: Voorkom dat u geplande taken hoeft te maken en beheren om gegevens te kopiëren door gegevens te migreren via Automation. Gebruik de uitgebreide integratie met Azure als een controle vlak voor het beheren en controleren van de voortgang van de migratie, inclusief selectieve gegevens replicatie, Hive-meta gegevens, gegevens beveiliging en vertrouwelijkheid.

- **Efficiëntie**: Houd hoge door Voer en prestaties en schaal eenvoudig naar Big data volumes. Met het beheer van bandbreedte gebruik kunt u ervoor zorgen dat u aan uw migratie doelen kunt voldoen zonder dat dit van invloed is op andere systeem bewerkingen.

## <a name="migrate-big-data-faster-without-risk"></a>big data sneller migreren zonder risico

De eerste service van het WANdisco LiveData platform voor Azure is [LiveData Migrator voor Azure](https://www.wandisco.com/products/livedata-migrator-for-azure). een oplossing voor het migreren van actief gebruikte gegevens van on-premises omgevingen naar Azure Storage. LiveData Migrator voor Azure is volledig ingericht en beheerd door de Azure Portal of Azure CLI, en naast uw Hadoop-cluster on-premises, zonder enige configuratie wijziging, toepassings wijzigingen of service wordt opnieuw gestart om direct te beginnen met het migreren van gegevens.

> [!div class="mx-imgBorder"]
> ![LiveData-Migrator voor Azure-architectuur](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

Big Data-migraties kunnen ingewikkeld en lastig zijn. Het is niet mogelijk om PETA bytes aan gegevens te verplaatsen zonder de bedrijfs activiteiten te onderbreken. [LiveData Migrator voor Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) biedt eenvoudige implementatie en kan een LiveData-service met continue gegevens migratie en replicatie tot stand brengen terwijl toepassingen de gegevens die worden gemigreerd, lezen, schrijven en wijzigen.

Het uitvoeren van een migratie is net zo eenvoudig als de volgende drie stappen:

1. Richt het LiveData-Migrator-exemplaar in van de Azure Portal naar uw on-premises Hadoop-cluster. Er is geen cluster wijziging of downtime nodig en toepassingen kunnen blijven werken.

   > [!div class="mx-imgBorder"]
   >![Een LiveData-Migrator-exemplaar maken](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Definieer het opslag account voor het doel Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   >![Een LiveData-Migrator doel maken](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Definieer de locatie van de gegevens die u wilt migreren, bijvoorbeeld: `/user/hive/warehouse` , en start de migratie.

   > [!div class="mx-imgBorder"]
   > ![Een migratie van een LiveData-migratie maken](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Bewaak de voortgang van de migratie met behulp van standaard Azure-hulpprogram ma's, waaronder Azure CLI en Azure Portal, en blijf uw on-premises omgeving blijven gebruiken. Voordat u begint, moet u deze [vereisten](https://docs.wandisco.com/live-data-platform/docs/prereq/)controleren.

## <a name="replicate-data-under-active-change"></a>Gegevens repliceren onder actieve wijziging

Grootschalige migraties van on-premises gegevens meren naar Azure moeten toepassingen testen en valideren. U kunt dit doen zonder het risico van het introduceren van gegevens wijzigingen waardoor meerdere bronnen van een waarheid worden gemaakt die niet eenvoudig kunnen worden afgestemd om Risico's te elimineren en de kosten van de overstap naar Azure te minimaliseren. [LiveData-vlak voor Azure](https://www.wandisco.com/products/livedata-plane-for-azure) maakt gebruik van de coördinatie-Engine technologie van WANdisco om deze problemen op te lossen.

> [!div class="mx-imgBorder"]
> ![LiveData-vlak voor Azure-architectuur](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Houd uw gegevens consistent in on-premises Hadoop-clusters en Azure Storage met LiveData-vlak voor Azure na de initiële migratie:

1. Richt LiveData-vlak in voor Azure on-premises en in azure, vanaf de Azure Portal. Er zijn geen wijzigingen in de toepassing vereist.
2. Configureer replicatie regels die betrekking hebben op de gegevens locaties die u consistent wilt blijven, bijvoorbeeld: `/user/contoso/sales/region/WA` .
3. Voer toepassingen uit die gegevens op een van beide locaties openen en wijzigen als een Hadoop-compatibel bestands systeem dat u nodig hebt.

LiveData-vlak voor Azure houdt uw gegevens consistent zonder dat er aanzienlijke overhead wordt toegepast op cluster bewerkingen of toepassings prestaties. Gegevens wijzigen of opnemen terwijl alle wijzigingen consistent worden gerepliceerd.

## <a name="next-steps"></a>Volgende stappen

- Het [LiveData-platform voor Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) voor Azure wordt gebruikt als elke andere Azure-resource en is nu beschikbaar in de preview-versie. 

- Meer informatie over de [vereisten](https://docs.wandisco.com/live-data-platform/docs/prereq/), het plannen van de migratie en het snel volt ooien van een grootschalige migratie met LiveData Migrator voor Azure.

- Probeer de LiveData-Migrator uit zonder gebruik te maken van een on-premises Hadoop-cluster met behulp van de- [sandbox HDFS](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/).

## <a name="see-also"></a>Zie ook

- [LiveData-Migrator voor Azure op Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [LiveData-vlak voor Azure op Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData-Migrator voor Azure-abonnementen en-prijzen](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [LiveData-vlak voor Azure-abonnementen en-prijzen](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [LiveData-platform voor veelgestelde vragen over Azure](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Bekende problemen met LiveData platform voor Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Inleiding tot Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)