---
title: Veelgestelde vragen over Azure SQL Database grootschalige
description: Antwoorden op veelgestelde vragen klanten vragen over een data base in SQL Database in de grootschalige-service laag, meestal een grootschalige-data base genoemd.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 02/03/2021
ms.openlocfilehash: c6faa1703b0935d66d291281f33027b3a66a59d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526819"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Veelgestelde vragen over Azure SQL Database grootschalige
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u antwoorden op veelgestelde vragen voor klanten die rekening houden met een data base in Azure SQL Database de grootschalige-servicelaag, waarnaar wordt verwezen als alleen grootschalige in de rest van deze veelgestelde vragen. In dit artikel worden de scenario's beschreven die grootschalige ondersteunt en de functies die compatibel zijn met grootschalige.

- Deze veelgestelde vragen zijn bedoeld voor lezers die een korte uitleg hebben over de grootschalige-service tier en op zoek zijn naar hun specifieke vragen en antwoorden.
- Deze veelgestelde vragen zijn niet bedoeld als een Guidebook of antwoorden op vragen over het gebruik van een grootschalige-data base. Voor een inleiding tot grootschalige raden wij u aan de documentatie over [Azure SQL database grootschalige](service-tier-hyperscale.md) te raadplegen.

## <a name="general-questions"></a>Algemene vragen

### <a name="what-is-a-hyperscale-database"></a>Wat is een grootschalige-data base?

Een grootschalige-data base is een data base in SQL Database in de grootschalige-servicelaag die wordt ondersteund door de grootschalige scale-out-opslag technologie. Een grootschalige-data base ondersteunt Maxi maal 100 TB aan gegevens en biedt hoge door Voer en prestaties, evenals snelle schaling om aan de werk belasting vereisten toe te passen. Schalen is transparant voor de toepassing – connectiviteit, query verwerking, enzovoort, zoals elke andere data base in Azure SQL Database.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Welke resource typen en aankoop modellen ondersteunen grootschalige

De grootschalige is alleen beschikbaar voor afzonderlijke data bases met behulp van het op vCore gebaseerde aankoop model in Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Hoe verschilt de grootschalige-servicelaag van de Algemeen-en Bedrijfskritiek-service lagen

De vCore-gebaseerde service lagen worden gedifferentieerd op basis van de beschik baarheid van de data base en het opslag type, de prestaties en de maximale grootte, zoals beschreven in de volgende tabel.

| | Resourcetype | Algemeen gebruik |  Hyperscale | Bedrijfskritiek |
|:---:|:---:|:---:|:---:|:---:|
| **Ideaal voor** |Alles|Biedt berekenings-en opslag opties voor budget gericht evenwicht.|De meeste zakelijke workloads. Opslag grootte automatisch schalen tot 100 TB, snelle verticale en horizontale reken schaal, snel data base terugzetten.|OLTP-toepassingen met hoge transactie snelheden en lage IO-latentie. Biedt de hoogste flexibiliteit voor fouten en snelle failovers met behulp van meerdere synchroon bijgewerkte replica's.|
|  **Resourcetype** ||Door SQL Database/SQL beheerd exemplaar | Individuele database | Door SQL Database/SQL beheerd exemplaar |
| **Reken grootte**|SQL Database * | 1 tot 80 vCores | 1 tot 80 vCores * | 1 tot 80 vCores |
| **Reken grootte**|SQL Managed Instance | 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Opslagtype** | Alles |Premium externe opslag (per instantie) | Niet-gekoppelde opslag met lokale SSD-cache (per instantie) | Super snelle lokale SSD-opslag (per instantie) |
| **Opslag grootte** | SQL Database *| 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| **Opslag grootte** | SQL Managed Instance  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **IOPS** | Individuele database | 500 IOPS per vCore met 7000 maximum aantal IOPS | Grootschalige is een architectuur met meerdere lagen met caching op meerdere niveaus. Effectief IOPS is afhankelijk van de werk belasting. | 5000 IOPS met 200.000 maximum aantal IOPS|
| **IOPS** | SQL Managed Instance | Is afhankelijk van de bestands grootte | N.v.t. | 1375 IOPS/vCore |
|**Beschikbaarheid**|Alles|1 replica, geen uitschalen van Lees bewerkingen, geen lokale cache | Meerdere replica's, Maxi maal 4 Lees scale-out, gedeeltelijke lokale cache | 3 replica's, 1 Lees scale-out, zone-redundante HA, volledige lokale opslag |
|**Back-ups**|Alles|RA-GRS, 7-35 dagen retentie (standaard 7 dagen)| RA-GRS, 7 dagen retentie, constant tijds duur herstel (PITR) | RA-GRS, 7-35 dagen retentie (standaard 7 dagen) |

\* Elastische Pools worden niet ondersteund in de grootschalige-servicelaag

### <a name="who-should-use-the-hyperscale-service-tier"></a>Wie moet de grootschalige-servicelaag gebruiken?

De grootschalige is bedoeld voor klanten die grote on-premises SQL Server data bases hebben en hun toepassingen willen moderniseren door over te stappen op de Cloud, of voor klanten die al Azure SQL Database gebruiken en de kans op database groei aanzienlijk wilt verg Roten. Grootschalige is ook bedoeld voor klanten die zowel hoge prestaties als hoge schaal baarheid verzamelen. Met grootschalige krijgt u het volgende:

- Database grootte tot 100 TB
- Snelle database back-ups, ongeacht de grootte van de data base (back-ups zijn gebaseerd op opslag momentopnamen)
- Snel data base terugzetten, ongeacht de grootte van de data base (herstel bewerkingen zijn afkomstig van opslag momentopnamen)
- Hogere logboek doorvoer, ongeacht de grootte van de data base en het aantal vCores
- Scale-out lezen met behulp van een of meer alleen-lezen replica's, gebruikt voor lees-offloading en als hot standby.
- Snel omhoog schalen van reken kracht, in constant tijd, om de zware werk belasting krachtiger te maken en vervolgens omlaag te schalen, in constante tijd. Dit is vergelijkbaar met het omhoog en omlaag schalen tussen een P6 en een P11, bijvoorbeeld, maar veel sneller, omdat dit geen grootte van de gegevens bewerking is.

### <a name="what-regions-currently-support-hyperscale"></a>Welke regio's ondersteunen momenteel grootschalige

De grootschalige is momenteel beschikbaar in de regio's onder [Azure SQL database grootschalige-overzicht](service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>Kan ik meerdere grootschalige-data bases per server maken

Ja. Zie [SQL database resource limieten voor één en gepoolde data bases op een server](resource-limits-logical-server.md)voor meer informatie en limieten voor het aantal grootschalige-data bases per server.

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Wat zijn de prestatie kenmerken van een grootschalige-data base?

De grootschalige-architectuur biedt hoge prestaties en door Voer, terwijl grote database grootten worden ondersteund.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Wat is de schaal baarheid van een grootschalige-data base

Grootschalige biedt snelle schaal baarheid op basis van de vraag van uw werk belasting.

- **Omhoog/omlaag schalen**

  Met grootschalige kunt u de primaire reken grootte in termen van resources, zoals CPU en geheugen, schalen en omlaag schalen, in constante tijd. Omdat de opslag wordt gedeeld, kunt u het formaat van de gegevens bewerking niet wijzigen.  
- **In-en uitschalen**

  Met grootschalige krijgt u ook de mogelijkheid om een of meer extra reken replica's in te richten die u kunt gebruiken om uw Lees aanvragen te verwerken. Dit betekent dat u deze extra reken replica's als alleen-lezen replica's kunt gebruiken om uw Lees werk belasting van de primaire Compute te offloaden. Naast alleen-lezen, fungeren deze replica's ook als hot standby in het geval van een failover van de primaire.

  Het inrichten van elk van deze extra reken replica's kan in constante tijd worden uitgevoerd en is een online bewerking. U kunt verbinding maken met deze extra Compute-replica's met het kenmerk alleen-lezen door het `ApplicationIntent` argument op uw Connection String in te stellen op `ReadOnly` . Verbindingen met de `ReadOnly` doel toepassing worden automatisch doorgestuurd naar een van de extra Compute-replica's met het kenmerk alleen-lezen.

## <a name="deep-dive-questions"></a>Diep gaande vragen

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>Kan ik grootschalige en afzonderlijke data bases op één server combi neren

Ja, dat kunt u.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Moet het programmeer model van de toepassing worden gewijzigd door grootschalige

Nee, het programmeer model van de toepassing blijft ongewijzigd. U gebruikt uw connection string zoals gebruikelijk en de andere regel matige manieren om te communiceren met uw grootschalige-data base.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Welk transactie isolatie niveau is de standaard waarde in een grootschalige-data base

Op de primaire replica is het standaard isolatie niveau van de trans actie RCSI (Lees doorgevoerde momentopname isolatie). Bij het lezen van scale-out secundaire replica's is het standaard isolatie niveau moment opname.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Kan ik mijn on-premises of IaaS-SQL Server licentie naar grootschalige

Ja, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is beschikbaar voor grootschalige. Elke SQL Server Standard kern kan worden toegewezen aan 1 grootschalige vCores. Elke SQL Server Enterprise kern kan worden toegewezen aan 4 grootschalige vCores. U hebt geen SQL-licentie nodig voor secundaire replica's. De Azure Hybrid Benefit prijs wordt automatisch toegepast voor het lezen van scale-out (secundaire) replica's.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Wat voor soort workloads grootschalige is ontworpen voor

Grootschalige ondersteunt alle SQL Server workloads, maar is voornamelijk geoptimaliseerd voor OLTP. U kunt ook Hybrid (HTAP) en analytische werk belastingen (Data Mart) opnemen.

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>Hoe kan ik kiezen tussen Azure Synapse Analytics en Azure SQL Database grootschalige

Als u op dit moment interactieve Analytics-query's uitvoert met SQL Server als Data Warehouse, is grootschalige een fantastische optie omdat u een kleine en middel grote data warehouse kunt hosten (zoals een aantal TB tot 100 TB) tegen lagere kosten, en u kunt uw SQL Server Data Warehouse-workloads migreren naar grootschalige met minimale T-SQL-code wijzigingen.

Als u gegevens analyse uitvoert op een grote schaal met complexe query's en langere opname snelheden van meer dan 100 MB/s, of met behulp van PDW (parallel data warehouse), Teradata of andere data warehouses met een enorme parallelle verwerking (MPP), is Azure Synapse Analytics mogelijk de beste keuze.
  
## <a name="hyperscale-compute-questions"></a>Vragen over grootschalige compute

### <a name="can-i-pause-my-compute-at-any-time"></a>Kan ik mijn Compute op elk gewenst moment onderbreken

Op dit moment kunt u uw reken kracht en aantal replica's echter omlaag schalen om de kosten te verlagen tijdens niet-piek tijden.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Kan ik een reken replica met extra RAM inrichten voor mijn geheugenintensieve werk belasting

Nee. Als u meer RAM-geheugen wilt ontvangen, moet u een upgrade uitvoeren naar een hogere reken grootte. Zie [grootschalige Storage and Compute sizes](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)(Engelstalig) voor meer informatie.

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Kan ik meerdere reken replica's van verschillende grootten inrichten

Nee.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Hoeveel Lees Scale-outs replica's worden ondersteund

De grootschalige-data bases worden standaard gemaakt met één Read scale-out replica (twee replica's, inclusief primair). U kunt het aantal alleen-lezen replica's tussen 0 en 4 schalen met behulp van [Azure Portal](https://portal.azure.com) of [rest API](/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Voor hoge Beschik baarheid moet ik extra reken replica's inrichten

In grootschalige-data bases wordt gegevens tolerantie op opslag niveau gegeven. U hebt slechts één replica nodig om tolerantie te bieden. Wanneer de compute replica niet actief is, wordt automatisch een nieuwe replica gemaakt zonder verlies van gegevens.

Als er echter maar één replica is, kan het enige tijd duren voordat de lokale cache in de nieuwe replica is gemaakt na een failover. Tijdens de fase van het opnieuw samen stellen van de cache worden gegevens rechtstreeks van de pagina servers opgehaald, wat resulteert in hogere opslag latentie en gedegradeerde query prestaties.

Voor bedrijfskritische apps waarvoor hoge Beschik baarheid met minimale failover-impact is vereist, moet u ten minste twee reken replica's inrichten, waaronder de primaire Compute replica. Dit is de standaard configuratie. Op die manier is er een hot standby-replica beschikbaar die fungeert als een failover-doel.

## <a name="data-size-and-storage-questions"></a>Vragen over gegevens grootte en opslag

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Wat is de maximale grootte van de data base die wordt ondersteund met grootschalige

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Wat is de grootte van het transactie logboek met grootschalige

Het transactie logboek met grootschalige is nagenoeg oneindig. U hoeft zich geen zorgen te maken over het uitvoeren van de logboek ruimte op een systeem met een hoge door Voer van het logboek. De snelheid voor het genereren van het logboek kan echter worden beperkt om workloads op agressieve basis te schrijven. De piek waarde voor het genereren van het aantal Logboeken is 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>`tempdb`Schaalt mijn data base groeit

Uw `tempdb` Data Base bevindt zich op lokale SSD-opslag en wordt proportioneel aangepast aan de reken grootte die u hebt ingericht. Uw `tempdb` is geoptimaliseerd om maximale prestatie voordelen te bieden. `tempdb` grootte kan niet worden geconfigureerd en wordt voor u beheerd.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Neemt de grootte van de data base automatisch toe of moet ik de grootte van de gegevens bestanden beheren

De grootte van de data base neemt automatisch toe tijdens het invoegen/opnemen van meer gegevens.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Wat is de kleinste grootte van de data base die grootschalige ondersteunt of begint met

40 GB. Een grootschalige-data base wordt gemaakt met een begin grootte van 10 GB. Vervolgens begint het te groeien met 10 GB om de 10 minuten, totdat de grootte van 40 GB wordt bereikt. Elk van deze 10 GB-segmenten wordt toegewezen in een andere pagina Server om meer IOPS en een hogere I/O-parallellisme te bieden. Vanwege deze optimalisatie, zelfs als u de oorspronkelijke grootte van de data base kleiner dan 40 GB kiest, wordt de data base automatisch uitgebreid tot ten minste 40 GB.

### <a name="in-what-increments-does-my-database-size-grow"></a>In welke stappen groeit de grootte van mijn data base

Elk gegevens bestand neemt toe met 10 GB. Meerdere gegevens bestanden kunnen tegelijkertijd groeien.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Is de opslag in grootschalige lokaal of extern

In grootschalige worden gegevens bestanden opgeslagen in azure Standard-opslag. Gegevens worden volledig in de cache opgeslagen op lokale SSD-opslag, op pagina servers die zich dicht bij de reken replica's bevinden. Daarnaast hebben reken replica's gegevens caches op lokale SSD en in het geheugen, om de frequentie van het ophalen van gegevens van externe pagina servers te verminderen.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kan ik bestanden of bestands groepen beheren of definiëren met grootschalige

Nee. Gegevens bestanden worden automatisch toegevoegd. De algemene redenen voor het maken van extra bestands groepen zijn niet van toepassing in de grootschalige-opslag architectuur.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kan ik een vaste limiet voor de gegevens groei van mijn data base inrichten

Nee.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Hoe worden gegevens bestanden ingedeeld met grootschalige

De gegevens bestanden worden beheerd door pagina servers, met één pagina server per gegevens bestand. Wanneer de gegevens grootte groeit, worden gegevens bestanden en gekoppelde pagina servers toegevoegd.

### <a name="is-database-shrink-supported"></a>Wordt database verkleinen ondersteund

Nee.

### <a name="is-data-compression-supported"></a>Wordt gegevens compressie ondersteund

Ja, met inbegrip van rij-, pagina-en column Store-compressie.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Als ik een enorme tabel heb, worden mijn tabel gegevens verdeeld over meerdere gegevens bestanden

Ja. De gegevens pagina's die aan een bepaalde tabel zijn gekoppeld, kunnen eindigen op meerdere gegevens bestanden, die allemaal deel uitmaken van dezelfde bestands groep. SQL Server maakt gebruik van [proportionele strategie](/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) voor het distribueren van gegevens over gegevens bestanden.

## <a name="data-migration-questions"></a>Vragen over gegevens migratie

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>Kan ik mijn bestaande data bases in Azure SQL Database verplaatsen naar de servicelaag grootschalige

Ja. U kunt uw bestaande data bases in Azure SQL Database verplaatsen naar grootschalige. Dit is een eenrichtings migratie. U kunt geen data bases van grootschalige naar een andere servicelaag verplaatsen. Voor testen van concept (POCs) raden wij u aan een kopie te maken van de data base en de kopie te migreren naar grootschalige. 

De tijd die nodig is om een bestaande Data Base te verplaatsen naar grootschalige, bestaat uit de tijd voor het kopiëren van gegevens en de tijd voor het opnieuw afspelen van de wijzigingen die in de bron database zijn aangebracht tijdens het kopiëren van gegevens. De tijd voor het kopiëren van gegevens is evenredig met de gegevens grootte. De tijd voor het opnieuw afspelen van wijzigingen is korter als de verplaatsing plaatsvindt tijdens een periode van een lage schrijf activiteit.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Kan ik mijn grootschalige-data bases verplaatsen naar andere service lagen

Nee. Op dit moment kunt u een grootschalige-data base niet naar een andere servicelaag verplaatsen.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Gaan er na de migratie naar de grootschalige-servicelaag enige functionaliteit of mogelijkheden verloren

Ja. Sommige Azure SQL Database-functies worden nog niet ondersteund in grootschalige, met inbegrip van maar niet beperkt tot een lange termijn back-up van het retentie. Nadat u uw data bases naar grootschalige hebt gemigreerd, werken deze functies niet meer.  We verwachten dat deze beperkingen tijdelijk zijn.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Kan ik mijn on-premises SQL Server Data Base of mijn SQL Server-data base in een virtuele machine in de Cloud verplaatsen naar grootschalige

Ja. U kunt alle bestaande migratie technologieën gebruiken om te migreren naar grootschalige, met inbegrip van transactionele replicatie en eventuele andere technologieën voor het verplaatsen van gegevens (Bulk Copy, Azure Data Factory, Azure Databricks, SSIS). Zie ook de [Azure database Migration service](../../dms/dms-overview.md), die veel migratie scenario's ondersteunt.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Wat is mijn downtime tijdens de migratie van een on-premises of virtuele-machine omgeving naar grootschalige, en hoe kan ik deze minimaliseren

De downtime voor migratie naar grootschalige is hetzelfde als de uitval tijd wanneer u uw data bases naar andere Azure SQL Database Service lagen migreert. U kunt [transactionele replicatie](replication-to-sql-database.md#data-migration-scenario
) gebruiken om de downtime te migreren voor data bases met een grootte van Maxi maal enkele TB. Voor zeer grote data bases (10 + TB) kunt u overwegen om gegevens te migreren met behulp van ADF, Spark of andere technologieën voor het verplaatsen van gegevens.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Hoe lang het duurt om de X-hoeveelheid gegevens naar grootschalige te brengen

Grootschalige is geschikt voor het gebruik van 100 MB/s met nieuwe/gewijzigde gegevens, maar de tijd die nodig is om gegevens te verplaatsen naar data bases in Azure SQL Database wordt ook beïnvloed door de beschik bare netwerk doorvoer, Lees snelheid van de bron en de service niveau doelstelling van de doel database.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>Kan ik gegevens lezen uit Blob Storage en snel laden (zoals poly base in azure Synapse Analytics)

U kunt een client toepassing gegevens laten lezen van Azure Storage en laden van gegevens in een grootschalige-data base (net als bij andere data bases in Azure SQL Database). Poly Base wordt momenteel niet ondersteund in Azure SQL Database. Als alternatief voor snelle belasting kunt u [Azure Data Factory](../../data-factory/index.yml)gebruiken, of een Spark-taak gebruiken in [Azure Databricks](/azure/azure-databricks/) met de Spark- [Connector voor SQL](spark-connector.md). De Spark-connector naar SQL ondersteunt bulksgewijze invoeging.

Het is ook mogelijk om gegevens te lezen uit een Azure Blob Store met behulp van BULK INSERT of OPENROWSET: [voor beelden van bulk toegang tot gegevens in Azure Blob Storage](/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Het eenvoudige herstel-of bulk logboek model wordt niet ondersteund in grootschalige. Een volledig herstel model is vereist voor een hoge Beschik baarheid en herstel naar een bepaald tijdstip. Grootschalige-logboek architectuur biedt echter betere gegevens opname frequentie vergeleken met andere Azure SQL Database-Service lagen.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Is grootschalige toegestaan om meerdere knoop punten in te richten voor parallelle opname van grote hoeveel heden gegevens

Nee. Grootschalige is een symmetrische SMP-architectuur (multi-processing) en is geen enorm parallelle verwerking (MPP) of een architectuur met meerdere masters. U kunt slechts meerdere replica's maken om alleen-lezen workloads uit te schalen.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Wat is de oudste SQL Server versie die wordt ondersteund voor migratie naar grootschalige

SQL Server 2005. Zie [migreren naar een enkele data base of een gegroepeerde Data Base](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database)voor meer informatie. Zie [compatibiliteits problemen met database migratie oplossen](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues)voor compatibiliteits problemen.

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Ondersteunt grootschalige migratie van andere gegevens bronnen, zoals Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 en andere database platforms

Ja. [Azure database Migration service](../../dms/dms-overview.md) ondersteunt veel migratie scenario's.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Vragen over bedrijfs continuïteit en herstel na nood gevallen

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Welke service overeenkomsten zijn voorzien voor een grootschalige-data base

Zie [Sla voor Azure SQL database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Extra secundaire reken replica's verhogen de beschik baarheid tot 99,99% voor een Data Base met twee of meer secundaire reken replica's.

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>Worden de database back-ups die voor mij worden beheerd door Azure SQL Database

Ja.

### <a name="how-often-are-the-database-backups-taken"></a>Hoe vaak de back-ups van de Data Base zijn gemaakt

Er zijn geen traditionele volledige, differentiële en logboek back-ups voor grootschalige-data bases. In plaats daarvan zijn er normale moment opnamen van gegevens bestanden. Het logboek dat wordt gegenereerd, wordt voor de geconfigureerde Bewaar periode eenvoudigweg bewaard, zodat het herstellen naar een bepaald tijdstip binnen de retentie periode kan worden hersteld.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Biedt grootschalige ondersteuning voor herstel naar een bepaald tijdstip

Ja.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Wat is het beoogde herstel punt (RPO)/Recovery Time doelstelling (RTO) voor het herstellen van de data base in grootschalige

De RPO is 0 min. De meeste herstel bewerkingen zijn binnen 60 minuten voltooid, ongeacht de grootte van de data base. De herstel tijd kan langer duren voor grotere data bases en als de Data Base voor en tot het herstel punt in de tijd aanzienlijke schrijf activiteiten heeft ondervonden.

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Is de database back-up van invloed op de reken prestaties op mijn primaire of secundaire replica's

Nee. Back-ups worden beheerd door het opslag subsysteem en maken gebruik van opslag momentopnamen. Ze hebben geen invloed op de werk belasting van de gebruiker.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Kan ik geo-herstel uitvoeren met een grootschalige-data base

Ja. Geo-Restore wordt volledig ondersteund. In tegens telling tot het herstel punt in de tijd vereist geo-Restore een omvang van gegevens bewerkingen. Gegevens bestanden worden parallel gekopieerd. de duur van deze bewerking is vooral afhankelijk van de grootte van het grootste bestand in de data base, in plaats van de totale database grootte. De geo-herstel tijd is aanzienlijk korter als de data base wordt hersteld in de Azure-regio die is [gekoppeld](../../best-practices-availability-paired-regions.md) aan de regio van de bron database.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Kan ik geo-replicatie met grootschalige-data base instellen

Momenteel niet.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Kan ik een back-up van een grootschalige-data base maken en deze herstellen op mijn on-premises server of op SQL Server in een VM

Nee. De opslag indeling voor grootschalige-data bases wijkt af van een uitgebrachte versie van SQL Server en u kunt geen back-ups beheren of er toegang toe hebben. Als u gegevens uit een grootschalige-Data Base wilt halen, kunt u gegevens extra heren met behulp van technologieën voor gegevens verplaatsing, d.w.z. Azure Data Factory, Azure Databricks, SSIS, enzovoort.

## <a name="cross-feature-questions"></a>Vragen over meerdere functies

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Gaan er na de migratie naar de grootschalige-servicelaag enige functionaliteit of mogelijkheden verloren

Ja. Sommige Azure SQL Database-functies worden niet ondersteund in grootschalige, waaronder maar niet beperkt tot een lange termijn back-up van het bewaren. Nadat u uw data bases naar grootschalige hebt gemigreerd, werken deze functies niet meer.

### <a name="will-polybase-work-with-hyperscale"></a>Werkt poly Base met grootschalige

Nee. Poly Base wordt niet ondersteund in Azure SQL Database.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Biedt grootschalige ondersteuning voor R en python

Momenteel niet.

### <a name="are-compute-nodes-containerized"></a>Worden reken knooppunten container

Nee. Grootschalige processen worden uitgevoerd op [service Fabric](https://azure.microsoft.com/services/service-fabric/) knoop punten (vm's), niet in containers.

## <a name="performance-questions"></a>Vragen over prestaties

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Hoeveel schrijf doorvoer kan ik pushen in een grootschalige-data base

De doorvoer capaciteit van het transactie logboek is ingesteld op 100 MB/s voor elke grootschalige-reken grootte. De mogelijkheid om dit aantal te bereiken, is afhankelijk van meerdere factoren, zoals het type werk belasting, de client configuratie en de juiste reken capaciteit op de primaire Compute-replica om het logboek op basis van dit aantal te maken.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Hoeveel IOPS krijg ik op de grootste compute

IOPS en IO-latentie variëren afhankelijk van de werkbelasting patronen. Als de gegevens die worden geopend, worden opgeslagen in de cache op de compute-replica, worden vergelijk bare i/o-prestaties weer gegeven als met de lokale SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Wordt mijn door Voer beïnvloed door back-ups

Nee. De compute wordt losgekoppeld van de opslaglaag. Dit elimineert de prestaties van de back-up.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Wordt de door Voer beïnvloed als ik extra reken replica's richt

Omdat de opslag wordt gedeeld en er geen directe fysieke replicatie plaatsvindt tussen primaire en secundaire reken replica's, wordt de door Voer op de primaire replica niet rechtstreeks beïnvloed door het toevoegen van secundaire replica's. We kunnen er echter voor zorgen dat continue agressieve werk belasting op de primaire regel wordt toegepast, zodat het logboek van toepassing is op secundaire replica's en pagina servers om te voor komen dat de Lees prestaties van secundaire replica's worden vertraagd.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Hoe kan ik problemen vaststellen en problemen oplossen in een grootschalige-data base

Voor de meeste prestatie problemen, met name de functies die niet zijn geroot tijdens de opslag prestaties, zijn veelvoorkomende stappen voor SQL Diagnostic en probleem oplossing van toepassing. Zie [SQL grootschalige Performance Troubleshooting Diagnostics](hyperscale-performance-diagnostics.md)(Engelstalig) voor grootschalige-specifieke opslag diagnose.

## <a name="scalability-questions"></a>Vragen over schaalbaarheid

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Hoe lang duurt het om een berekenings replica omhoog en omlaag te schalen

Het schalen van de reken capaciteit duurt meestal Maxi maal 2 minuten, ongeacht de grootte van de gegevens.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Is mijn data base offline terwijl de bewerking omhoog/omlaag schalen wordt uitgevoerd

Nee. U kunt omhoog en omlaag schalen om online te gaan.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Moet ik verwachten dat er verbinding wordt gemaakt wanneer de schaal bewerkingen worden uitgevoerd

Als u omhoog of omlaag schaalt, worden de bestaande verbindingen verwijderd wanneer er aan het einde van de schaal bewerking een failover wordt uitgevoerd. Het toevoegen van secundaire replica's resulteert niet in een verbroken verbinding.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Wordt het omhoog en omlaag schalen van reken replica's automatisch of door eind gebruikers geactiveerde bewerking

Eind gebruiker. Niet automatisch.  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>Worden de grootte van mijn `tempdb` Data Base en RBPEX-cache ook groter naarmate de reken kracht omhoog wordt geschaald

Ja. De `tempdb` cache grootte van de data base en de [RBPEX](service-tier-hyperscale.md#distributed-functions-architecture) op reken knooppunten worden automatisch geschaald naarmate het aantal kern geheugens wordt verhoogd.

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Kan ik meerdere primaire reken replica's inrichten, zoals een systeem met meerdere masters, waarbij meerdere primaire Compute Heads een hoger niveau van gelijktijdigheid kunnen hebben

Nee. Alleen de primaire Compute replica accepteert Lees-en schrijf aanvragen. Secundaire reken replica's accepteren alleen alleen-lezen-aanvragen.

## <a name="read-scale-out-questions"></a>Vragen over uitschalen lezen

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Hoeveel secundaire reken replica's kan ik inrichten

Er wordt standaard één secundaire replica voor grootschalige-data bases gemaakt. Als u het aantal replica's wilt aanpassen, kunt u dit doen met behulp van [Azure Portal](https://portal.azure.com) of [rest API](/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Hoe kan ik verbinding maken met deze secundaire reken replica's

U kunt verbinding maken met deze extra Compute-replica's met het kenmerk alleen-lezen door het `ApplicationIntent` argument op uw Connection String in te stellen op `ReadOnly` . Alle verbindingen die zijn gemarkeerd met `ReadOnly` , worden automatisch doorgestuurd naar een van de extra alleen-lezen Compute-replica's. Zie [alleen-lezen Replica's gebruiken om werk belastingen met alleen-lezen query's te offloaden](read-scale-out.md)voor meer informatie.

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Hoe kan ik valideren of de verbinding met de secundaire Compute-replica is geslaagd met behulp van SSMS of andere client hulpprogramma's?

U kunt de volgende T-SQL-query uitvoeren: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` .
Het resultaat is `READ_ONLY` Als u verbonden bent met een secundaire replica met het kenmerk alleen-lezen en `READ_WRITE` Als u bent verbonden met de primaire replica. Houd er rekening mee dat de database context moet worden ingesteld op de naam van de grootschalige-data base, niet op de- `master` Data Base.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Kan ik een toegewezen eind punt maken voor een uitschaal replica

Nee. U kunt alleen verbinding maken met het lezen van scale-out replica's door op te geven `ApplicationIntent=ReadOnly` .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Voert het systeem intelligente taak verdeling van de werk belasting lezen uit

Nee. Een nieuwe verbinding met alleen-lezen intentie wordt omgeleid naar een wille keurige uitschaal replica.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Kan ik de secundaire reken replica's onafhankelijk van de primaire replica omhoog/omlaag schalen

Nee. De secundaire Compute replica wordt ook gebruikt als failover-doelen met hoge Beschik baarheid, zodat ze dezelfde configuratie moeten hebben als de primaire voor het leveren van verwachte prestaties na een failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Krijg ik een andere `tempdb` grootte voor mijn primaire Compute en mijn extra secundaire reken replica's

Nee. Uw `tempdb` Data Base is geconfigureerd op basis van de inrichting van de berekenings grootte, uw secundaire reken replica's hebben dezelfde grootte als de primaire compute.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Kan ik indexen en weer gaven toevoegen aan mijn secundaire reken replica's

Nee. Grootschalige-data bases hebben gedeelde opslag, wat betekent dat alle reken replica's dezelfde tabellen, indexen en weer gaven zien. Als u aanvullende indexen wilt optimaliseren voor lees bewerkingen op de secundaire, moet u deze toevoegen aan de primaire.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Hoeveel vertraging er tussen de primaire en secundaire Compute-replica's plaatsvindt

Gegevens latentie vanaf het moment dat een trans actie wordt doorgevoerd op de primaire en de tijd die het kan worden gelezen op een secundair, is afhankelijk van de huidige frequentie voor het genereren van het logboek, de transactie grootte, de belasting van de replica en andere factoren. Typische gegevens latentie voor kleine trans acties is in tien tallen milliseconden, maar er is geen bovengrens voor de gegevens latentie. Gegevens op een bepaalde secundaire replica zijn altijd transactioneel consistent. Op een bepaald moment kan gegevens latentie echter verschillen voor verschillende secundaire replica's. Werk belastingen die direct moeten worden doorgevoerd, moeten worden uitgevoerd op de primaire replica.

## <a name="next-steps"></a>Volgende stappen

Zie [grootschalige service tier](service-tier-hyperscale.md)(Engelstalig) voor meer informatie over de grootschalige.