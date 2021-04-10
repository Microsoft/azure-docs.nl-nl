---
title: Bronlimieten
titleSuffix: Azure SQL Managed Instance
description: Dit artikel bevat een overzicht van de resource limieten voor Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: ee0c673027892e6d3ee55b6ec5f67a30d88be683
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780341"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Overzicht van resourcelimieten voor Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dit artikel bevat een overzicht van de technische kenmerken en resource limieten voor Azure SQL Managed instance en bevat informatie over het aanvragen van een verhoging van deze limieten.

> [!NOTE]
> Zie voor verschillen in ondersteunde functies en T-SQL-instructies [functie verschillen](../database/features-comparison.md) en [ondersteuning voor t-SQL-instructie](transact-sql-tsql-differences-sql-server.md). Zie de [vergelijking](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison)van de servicelaag voor algemene verschillen tussen de service lagen voor Azure SQL database en SQL Managed instance.

## <a name="hardware-generation-characteristics"></a>Kenmerken voor het genereren van hardware

SQL Managed instance heeft kenmerken en resource limieten die afhankelijk zijn van de onderliggende infra structuur en architectuur. Een door SQL beheerd exemplaar kan worden geïmplementeerd op twee hardware-generaties: Gen4 en GEN5. Hardware-generaties hebben verschillende kenmerken, zoals wordt beschreven in de volgende tabel:

|   | **Gen4** | **GEN5** |
| --- | --- | --- |
| **Hardware** | Intel® E5-2673 v3 (Haswell) 2,4 GHz-processors, Attached SSD vCore = 1 PP (fysieke kern) | Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) en Intel® 8272CL (trapsgewijze Lake) 2,5 GHz processors, Fast NVMe SSD, vCore = 1 LP (Hyper-Thread) |
| **Aantal vCores** | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| **Maxi maal geheugen (geheugen/kern percentage)** | 7 GB per vCore<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. | 5,1 GB per vCore<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. |
| **Maxi maal In-Memory OLTP-geheugen** | Limiet voor instanties: 1-1,5 GB per vCore| Limiet voor instanties: 0,8-1,65 GB per vCore |
| **Maximum aantal gereserveerde exemplaren** |  Algemeen: 8 TB<br/>Bedrijfskritiek: 1 TB | Algemeen: 8 TB<br/> Bedrijfskritiek 1 TB, 2 TB of 4 TB afhankelijk van het aantal kernen |

> [!IMPORTANT]
> - Gen4-hardware wordt gefaseerd uitgevoerd en is niet meer beschikbaar voor nieuwe implementaties. Alle nieuwe exemplaren van SQL Managed instance moeten worden geïmplementeerd op GEN5-hardware.
> - Overweeg om [uw exemplaar van SQL Managed instance te verplaatsen naar Gen 5](../database/service-tiers-vcore.md) -hardware, zodat u een breder scala aan vCore-en opslag schaal baarheid, versneld netwerken, beste IO-prestaties en minimale latentie kunt ervaren.

### <a name="in-memory-oltp-available-space"></a>Beschik bare ruimte in geheugen voor OLTP 

De hoeveelheid OLTP-ruimte in het geheugen in [bedrijfskritiek](../database/service-tier-business-critical.md) servicelaag is afhankelijk van het aantal vCores en de generatie van hardware. De volgende tabel geeft een lijst van geheugen limieten die kunnen worden gebruikt voor in-Memory OLTP-objecten.

| OLTP-ruimte in het geheugen  | **GEN5** | **Gen4** |
| --- | --- | --- |
| 4 vCores  | 3,14 GB | |   
| 8 vCores  | 6,28 GB | 8 GB |
| 16 vCores | 15,77 GB | 20 GB |
| 24 vCores | 25,25 GB | 36 GB |
| 32 vCores | 37,94 GB | |
| 40 vCores | 52,23 GB | |
| 64 vCores | 99,9 GB    | |
| 80 vCores | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Kenmerken van servicelaag

SQL Managed instance heeft twee service lagen: [Algemeen](../database/service-tier-general-purpose.md) en [bedrijfskritiek](../database/service-tier-business-critical.md). Deze lagen bieden [verschillende mogelijkheden](../database/service-tiers-general-purpose-business-critical.md), zoals wordt beschreven in de volgende tabel.

> [!Important]
> Bedrijfskritiek service-laag biedt een extra ingebouwde kopie van de SQL Managed instance (secundaire replica) die kan worden gebruikt voor alleen-lezen werk belasting. Als u lees-en schrijf query's en query's met het kenmerk alleen-lezen/analyse kunt onderscheiden, krijgt u twee maal de vCores en het geheugen voor dezelfde prijs. De secundaire replica kan een paar seconden achter het primaire exemplaar vertraging hebben, zodat het is ontworpen voor het offloaden van rapportage-en analytische workloads die niet de exacte huidige status van gegevens nodig hebben. In de onderstaande tabel zijn **alleen-lezen query's** de query's die worden uitgevoerd op de secundaire replica.

| **Functie** | **Algemeen doel** | **Bedrijfskritiek** |
| --- | --- | --- |
| Aantal vCores\* | Gen4:8, 16, 24<br/>GEN5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4:8, 16, 24 <br/> GEN5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Hetzelfde aantal vCores is toegewezen voor alleen-lezen query's. |
| Maxi maal geheugen | Gen4:56 GB-168 GB (7GB/vCore)<br/>GEN5:20,4 GB-408 GB (5,1 GB/vCore)<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. | Gen4:56 GB-168 GB (7GB/vCore)<br/>GEN5:20,4 GB-408 GB (5,1 GB/vCore) voor lees-en schrijf query's<br/>+ extra 20,4 GB-408 GB (5,1 GB/vCore) voor alleen-lezen query's.<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. |
| Maximale opslag grootte van exemplaar (gereserveerd) | -2 TB voor 4 vCores (alleen GEN5)<br/>-8 TB voor andere grootten | Gen4:1 TB <br/> GEN5 <br/>-1 TB voor 4, 8, 16 vCores<br/>-2 TB voor 24 vCores<br/>-4 TB voor 32, 40, 64, 80 vCores |
| Maximale databasegrootte | Tot momenteel beschik bare instantie grootte (Maxi maal 2 TB-8 TB afhankelijk van het aantal vCores). | Maxi maal beschik bare instantie grootte (Maxi maal 1 TB-4 TB, afhankelijk van het aantal vCores). |
| Maximale grootte van tempDB | Beperkt tot 24 GB/vCore (96-1.920 GB) en momenteel beschik bare instantie opslag grootte.<br/>Voeg meer vCores toe om meer TempDB-ruimte te krijgen.<br/> De grootte van het logboek bestand is beperkt tot 120 GB.| Maxi maal beschik bare opslag grootte van exemplaar. |
| Maximum aantal data bases per exemplaar | 100 gebruikers databases, tenzij de opslag limiet voor het exemplaar is bereikt. | 100 gebruikers databases, tenzij de opslag limiet voor het exemplaar is bereikt. |
| Maximum aantal database bestanden per exemplaar | Maxi maal 280, tenzij de limiet voor de opslag ruimte van het exemplaar of het bereik voor de [toewijzing van Azure Premium-schijf opslag](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) is bereikt. | 32.767 bestanden per data base, tenzij de maximale opslag grootte van het exemplaar is bereikt. |
| Maximale grootte van het gegevens bestand | Beperkt tot de momenteel beschik bare opslag grootte van het exemplaar (Maxi maal 2 TB-8 TB) en de [opslag ruimte voor Azure Premium-schijven](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). | Beperkt tot de momenteel beschik bare opslag grootte van het exemplaar (Maxi maal 1 TB-4 TB). |
| Maximale grootte van logboek bestand | Beperkt tot 2 TB en momenteel beschik bare exemplaar opslag grootte. | Beperkt tot 2 TB en momenteel beschik bare exemplaar opslag grootte. |
| Gegevens/logboek IOPS (benadering) | Maxi maal 30-40 K IOPS per exemplaar *, 500-7500 per bestand<br/>\*[Bestands grootte verg Roten om meer IOPS te verkrijgen](#file-io-characteristics-in-general-purpose-tier)| 16 k-320 K (4000 IOPS/vCore)<br/>Voeg meer vCores toe om betere IO-prestaties te krijgen. |
| Doorvoer limiet voor schrijf bewerkingen in logboek (per instantie) | 3 MB/s per vCore<br/>Maxi maal 120 MB/s per instantie<br/>22-65 MB/s per data base<br/>\*[De bestands grootte verg Roten om betere IO-prestaties te krijgen](#file-io-characteristics-in-general-purpose-tier) | 4 MB/s per vCore<br/>Maxi maal 96 MB/s |
| Gegevens doorvoer (bij benadering) | 100-250 MB/s per bestand<br/>\*[De bestands grootte verg Roten om betere IO-prestaties te krijgen](#file-io-characteristics-in-general-purpose-tier) | Niet beperkt. |
| I/o-latentie van opslag (ongeveer) | 5-10 MS | 1-2 MS |
| OLTP in het geheugen | Niet ondersteund | Beschikbaar, [grootte is afhankelijk van het aantal vCore](#in-memory-oltp-available-space) |
| Maximum aantal sessies | 30.000 | 30.000 |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen) | Gen4: 210 * aantal vCores + 800<br>Gen5: 105 * aantal vCores + 800 | Gen4: 210 * vCore count + 800<br>Gen5: 105 * vCore count + 800 |
| [Alleen-lezen replica's](../database/read-scale-out.md) | 0 | 1 (inclusief prijs) |
| Reken isolatie | GEN5 wordt niet ondersteund omdat Algemeen exemplaren fysieke hardware kunnen delen met andere exemplaren<br/>Gen4 wordt niet ondersteund vanwege afschaffing|GEN5<br/>-ondersteund voor 40, 64, 80 vCores<br/>-niet ondersteund voor andere grootten<br/><br/>Gen4 wordt niet ondersteund vanwege afschaffing|


Enkele aanvullende overwegingen: 

- De **momenteel beschik bare opslag grootte voor instanties** is het verschil tussen de gereserveerde exemplaar grootte en de gebruikte opslag ruimte.
- De grootte van de gegevens en het logboek bestand in de gebruikers-en systeem databases zijn opgenomen in de opslag grootte van het exemplaar, vergeleken met de maximale opslag grootte. Gebruik de weer gave [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) systeem om de totale hoeveelheid gebruikte ruimte te bepalen op basis van data bases. Fouten logboeken worden niet persistent gemaakt en zijn niet opgenomen in de grootte. Back-ups worden niet opgenomen in de opslag grootte.
- De door Voer en IOPS in de laag Algemeen zijn ook afhankelijk van de [Bestands grootte](#file-io-characteristics-in-general-purpose-tier) die niet expliciet wordt beperkt door het SQL Managed instance.
  U kunt een andere Lees bare replica maken in een andere Azure-regio met behulp van [groepen voor automatische failover](../database/auto-failover-group-configure.md)
- Het maximum aantal IOPS is afhankelijk van de bestands indeling en de distributie van de werk belasting. Als u bijvoorbeeld 7 x 1 TB bestanden maakt met Maxi maal 5K IOPS per en 7 kleine bestanden (kleiner dan 128 GB) met 500 IOPS per exemplaar, kunt u 38500 IOPS per instantie (7x5000 + 7x500) verkrijgen als uw werk belasting alle bestanden kan gebruiken. Houd er rekening mee dat een aantal IOPS ook wordt gebruikt voor automatische back-ups.

In dit artikel vindt u meer informatie over de [resource limieten in SQL Managed instance Pools](instance-pools-overview.md#resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Bestands-IO-kenmerken in laag Algemeen

In het Algemeen servicelaag worden elk database bestand toegewezen IOPS en door Voer die afhankelijk zijn van de bestands grootte. Grotere bestanden krijgen meer IOPS en door voer. I/o-kenmerken van database bestanden worden weer gegeven in de volgende tabel:

| Bestandsgrootte | >= 0 en <= 128 GiB | >128 en <= 512 GiB | >0,5 en <= 1 TiB    | >1 en <= 2 TiB    | >2 en <= 4 TiB | >4 en <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per bestand       | 500   | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Door Voer per bestand | 100-MiB/s | 150-MiB/s | 200-MiB/s | 250 MiB/s | 250 MiB/s | 480-MiB/s | 

Als u een hoge IO-latentie krijgt bij een bepaald database bestand of als u ziet dat IOPS/door Voer de limiet bereikt, kunt u de prestaties verbeteren door [de bestands grootte te verg Roten](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Er is ook een limiet op exemplaar niveau voor de maximale schrijf doorvoer in het logboek (zie hierboven voor waarden, bijvoorbeeld 22 MB/s), zodat u het Maxi maal toegestane bestand niet in het logboek bestand kunt bereiken omdat u de limiet voor het aantal door Voer van het exemplaar bereikt.

## <a name="supported-regions"></a>Ondersteunde regio’s

Een SQL Managed instance kan alleen worden gemaakt in [ondersteunde regio's](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Als u een SQL Managed instance wilt maken in een regio die momenteel niet wordt ondersteund, kunt u [via de Azure Portal een ondersteunings aanvraag verzenden](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

SQL Managed instance biedt momenteel alleen ondersteuning voor de implementatie van de volgende typen abonnementen:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Cloud serviceprovider (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnementen met een maandelijks Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionale resource beperkingen

> [!Note]
> Voor de meest recente informatie over de beschik baarheid van regio's voor abonnementen, controleert u eerst op [een regio selecteren](https://aka.ms/sqlcapacity).

Ondersteunde abonnements typen kunnen een beperkt aantal resources per regio bevatten. SQL Managed instance heeft twee standaard limieten per Azure-regio (die op aanvraag kan worden verhoogd door een speciale [ondersteunings aanvraag in de Azure Portal](../database/quota-increase-request.md) te maken, afhankelijk van een type abonnements type:

- **Subnet limiet**: het maximum aantal subnetten waar exemplaren van een door SQL beheerd exemplaar worden geïmplementeerd in een enkele regio.
- **limiet vCore**: het maximum aantal vCore-eenheden dat kan worden geïmplementeerd in alle instanties in één regio. Eén GP-vCore maakt gebruik van één vCore-eenheid en één BC vCore neemt vier vCore eenheden in beslag. Het totale aantal exemplaren is niet beperkt zolang het zich binnen de limiet van de vCore-eenheid bevindt.

> [!Note]
> Deze limieten zijn standaard instellingen en niet van technische beperkingen. De limieten kunnen op aanvraag worden verhoogd door een speciale [ondersteunings aanvraag te maken in de Azure Portal](../database/quota-increase-request.md) als u meer exemplaren in de huidige regio nodig hebt. Als alternatief kunt u nieuwe exemplaren van SQL Managed instance in een andere Azure-regio maken zonder ondersteunings aanvragen te verzenden.

De volgende tabel bevat de **standaard regionale limieten** voor ondersteunde abonnements typen (standaard limieten kunnen worden uitgebreid met ondersteunings aanvraag die hieronder wordt beschreven):

|Abonnementstype| Maximum aantal subnetten voor SQL-beheerde exemplaren | Maximum aantal vCore-eenheden * |
| :---| :--- | :--- |
|Betalen naar gebruik|3|320|
|CSP |8 (15 inch in sommige regio's * *)|960 (1440 in sommige regio's * *)|
|Dev/test met betalen per gebruik|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 inch in sommige regio's * *)|960 (1440 in sommige regio's * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional en MSDN Platforms|2|32|

\* In het plannen van implementaties moet u rekening houden dat de service tier van Bedrijfskritiek (BC) vier (4) keer zoveel vCore capaciteit nodig heeft dan de servicelaag van Algemeen (GP). Bijvoorbeeld: 1 GP vCore = 1 vCore-eenheid en 1 BC vCore = 4 vCore. Als u de verbruiks analyse wilt vereenvoudigen met de standaard limieten, samenvatten we de vCore-eenheden in alle subnetten in de regio waar SQL Managed instance wordt geïmplementeerd en vergelijkt u de resultaten met de limieten van de exemplaar eenheid voor uw abonnements type. De limiet voor het **maximum aantal vCore-eenheden** is van toepassing op elk abonnement in een regio. Er is geen limiet per afzonderlijke subnetten, behalve dat de som van alle vCores die in meerdere subnetten zijn geïmplementeerd, kleiner of gelijk moet zijn aan het **maximum aantal vCore-eenheden**.

\*\* Grotere subnet-en vCore-limieten zijn beschikbaar in de volgende regio's: Australië-oost, VS-Oost, VS-Oost 2, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, Europa-west, VS-West 2.

> [!IMPORTANT]
> Als uw vCore-en subnet-limiet 0 is, betekent dit dat de standaard regionale limiet voor uw abonnements type niet is ingesteld. U kunt ook de aanvraag quotum verhogen gebruiken voor het verkrijgen van toegang tot het abonnement in een specifieke regio volgens dezelfde procedure: de vereiste vCore-en subnet-waarden opgeven.

## <a name="request-a-quota-increase"></a>Een quotum verhoging aanvragen

Als u meer exemplaren nodig hebt in uw huidige regio's, verzendt u een ondersteunings aanvraag om het quotum uit te breiden met behulp van de Azure Portal. Zie [aanvraag quotum verhogingen voor Azure SQL database](../database/quota-increase-request.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een SQL-beheerd exemplaar?](sql-managed-instance-paas-overview.md)voor meer informatie over SQL Managed instance.
- Zie [prijzen van SQL Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijs informatie.
- Zie [de Snelstartgids](instance-create-quickstart.md)voor meer informatie over het maken van uw eerste SQL Managed instance.
