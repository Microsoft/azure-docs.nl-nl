---
title: Diagnostische gegevens over prestaties in grootschalige
description: In dit artikel wordt beschreven hoe u grootschalige prestatie problemen in Azure SQL Database kunt oplossen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: ed31ff5d77b258d141a77fc174c2d5452adf7d01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791712"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnostische gegevens voor het oplossen van problemen met SQL grootschalige-prestaties
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Om prestatie problemen in een grootschalige-Data Base op te lossen, is de [algemene methoden voor het afstemmen van prestaties](monitor-tune-overview.md) op het Azure SQL database Compute-knoop punt het begin punt van een prestatie onderzoek. Gezien de [gedistribueerde architectuur](service-tier-hyperscale.md#distributed-functions-architecture) van grootschalige zijn aanvullende diagnostische gegevens echter toegevoegd om te helpen. In dit artikel worden grootschalige-specifieke diagnostische gegevens beschreven.

## <a name="log-rate-throttling-waits"></a>Wacht tijden voor het beperken van de logboek frequentie

Elk Azure SQL Database service niveau heeft limieten voor het genereren van de logboeken die worden afgedwongen via [log rate governance](resource-limits-logical-server.md#transaction-log-rate-governance). In grootschalige is de limiet voor het genereren van het logboek op dit moment ingesteld op 100 MB per seconde, ongeacht het service niveau. Er zijn echter momenten waarop de snelheid waarmee het logboek is gegenereerd op de primaire Compute-replica moet worden beperkt om de bezorgings-Sla's te behouden. Deze beperking treedt op wanneer een [pagina Server of een andere compute replica](service-tier-hyperscale.md#distributed-functions-architecture) significant achter het Toep assen van nieuwe logboek records van de logboek service.

De volgende wacht typen (in [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) beschrijven de redenen waarom de logboek frequentie kan worden beperkt op de primaire Compute replica:

|Wacht type    |Description                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Treedt op wanneer een grootschalige-data base het genereren van de snelheid van het primaire Compute-knooppunt logboek wordt beperkt vanwege een vertraagd logboek verbruik op pagina-Server (s).         |
|RBIO_RG_DESTAGE        | Treedt op wanneer de snelheid van het genereren van het logboek van de grootschalige-data base wordt beperkt vanwege een vertraagd logboek gebruik door de lange termijn logboek opslag.         |
|RBIO_RG_REPLICA        | Treedt op wanneer de snelheid van het genereren van het grootschalige-database registratie knooppunt wordt beperkt vanwege een vertraagd logboek verbruik door de Lees bare secundaire replica (s).         |
|RBIO_RG_LOCALDESTAGE   | Treedt op wanneer de snelheid van het genereren van het grootschalige-database registratie knooppunt wordt beperkt vanwege een vertraagd logboek verbruik door de logboek service.         |

## <a name="page-server-reads"></a>Lees bewerkingen pagina Server

De reken replica's slaan geen volledige kopie van de data base lokaal op in de cache. De gegevens die lokaal zijn voor de berekenings replica, worden opgeslagen in de buffer groep (in het geheugen) en in de lokale cache met RBPEX (bestendigere buffer pool extension) die een gedeeltelijk (niet-bedekte) cache van gegevens pagina's is. Deze lokale RBPEX-cache bevindt zich proportioneel in verhouding tot de berekenings grootte en is drie maal zo groot als het geheugen van de compute-laag. RBPEX is vergelijkbaar met de buffer groep waarin de meest gebruikte gegevens worden gebruikt. Elke pagina Server heeft daarentegen een dekking RBPEX cache voor het gedeelte van de data base dat wordt onderhouden.

Als er een lees bewerking wordt uitgevoerd op een berekenings replica en de gegevens niet in de buffer groep of de lokale RBPEX-cache bestaan, wordt een getPage (pageId, LSN)-functie aanroep uitgegeven en wordt de pagina opgehaald van de bijbehorende pagina Server. Lees bewerkingen van pagina servers zijn op afstand lezen en zijn daardoor trager dan lees bewerkingen van de lokale RBPEX. Bij het oplossen van problemen met betrekking tot de prestaties van IO, moeten we kunnen zien hoeveel IOs is uitgevoerd via relatief trage externe pagina-Server Lees bewerkingen.

Diverse dynamische beheerde weer gaven (Dmv's) en uitgebreide gebeurtenissen hebben kolommen en velden die het aantal externe Lees bewerkingen van een pagina Server opgeven, die kunnen worden vergeleken met het totale aantal lees bewerkingen. In query Store worden ook externe Lees bewerkingen vastgelegd als onderdeel van de statistieken van de uitvoerings tijd van de query.

- Kolommen voor het rapporteren van pagina-Server zijn beschikbaar in uitvoerings-Dmv's en catalogus weergaven, zoals:

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Lees bewerkingen voor pagina-Server worden toegevoegd aan de volgende uitgebreide gebeurtenissen:
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - query-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads worden toegevoegd aan het query plan XML voor de werkelijke plannen. Bijvoorbeeld:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Als u deze kenmerken wilt weer geven in het venster Eigenschappen van query plan, is SSMS 18,3 of hoger vereist.

## <a name="virtual-file-stats-and-io-accounting"></a>Statistieken voor het virtuele bestand en IO-Accounting

In Azure SQL Database is de [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF de belangrijkste manier om SQL database io te controleren. I/o-kenmerken in grootschalige verschillen vanwege de [gedistribueerde architectuur](service-tier-hyperscale.md#distributed-functions-architecture). In deze sectie richten we ons op IO (lees-en schrijf bewerkingen) naar gegevens bestanden zoals deze worden weer gegeven in deze DMF. In grootschalige komt elk gegevens bestand dat zichtbaar is in deze DMF overeen met een externe pagina Server. De RBPEX-cache die hier wordt vermeld, is een lokale SSD-cache, een niet-bedekkende cache op de compute replica.

### <a name="local-rbpex-cache-usage"></a>Lokaal RBPEX-cache gebruik

Lokale RBPEX-cache bestaat op de berekenings replica, op lokale SSD-opslag. Daarom is IO voor deze cache sneller dan IO voor externe pagina servers. Op dit moment heeft [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) in een grootschalige-Data Base een speciale rij waarmee de io wordt gerapporteerd voor de lokale RBPEX-cache op de compute replica. Deze rij heeft de waarde 0 voor zowel `database_id` als `file_id` kolommen. De query hieronder retourneert bijvoorbeeld RBPEX gebruiks statistieken sinds het opstarten van de data base.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Een verhouding van Lees bewerkingen die worden uitgevoerd op RBPEX naar geaggregeerde Lees bewerkingen die worden uitgevoerd voor alle andere gegevens bestanden, biedt de RBPEX-cache treffers.

### <a name="data-reads"></a>Gegevens lezen

- Wanneer Lees bewerkingen worden uitgegeven door de SQL Server data base-engine op een compute-replica, kunnen ze worden geleverd door de lokale RBPEX-cache of door externe pagina servers, of door een combi natie van de twee als er meerdere pagina's worden gelezen.
- Wanneer de reken replica sommige pagina's van een specifiek bestand leest, bijvoorbeeld file_id 1, als deze gegevens uitsluitend in de lokale RBPEX-cache zijn opgeslagen, wordt alle i/o voor deze Lees bewerking verwerkt op file_id 0 (RBPEX). Als een deel van die gegevens zich in de lokale RBPEX-cache bevindt en een deel ervan op een externe pagina server bevindt, wordt de i/o in rekening gezet met file_id 0 voor het onderdeel dat wordt aangeboden vanuit RBPEX, en wordt het onderdeel dat wordt aangeboden vanaf de externe pagina Server, verwerkt naar file_id 1.
- Wanneer een berekenings replica een pagina van een bepaalde [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) van een pagina server aanvraagt en de pagina Server niet heeft geresulteerd in het aangevraagde LSN, wordt de Lees bewerking op de reken replica gewacht totdat de pagina wordt geretourneerd naar de berekenings replica. Voor alle Lees bewerkingen van een pagina op de compute-replica ziet u het PAGEIOLATCH_ * wait-type als het wacht op die i/o. In grootschalige bevat deze wacht tijd zowel de tijd die nodig is voor het opvangen van de aangevraagde pagina op de pagina Server naar de vereiste LSN, en de benodigde tijd voor het overzetten van de pagina van de pagina Server naar de compute replica.
- Grote Lees bewerkingen, zoals lezen-vooruit, worden vaak uitgevoerd met behulp van de [Lees bewerkingen van ' sprei ding-gather '](/sql/relational-databases/reading-pages/). Op deze manier kunnen Lees bewerkingen van Maxi maal 4 MB aan pagina's tegelijk worden beschouwd als één Lees bewerking in de SQL Server data base-engine. Als er echter gegevens worden gelezen in RBPEX, worden deze Lees bewerkingen beschouwd als meerdere afzonderlijke 8 KB-Lees bewerkingen, omdat de buffer groep en RBPEX altijd 8 KB-pagina's gebruiken. Als gevolg hiervan kan het aantal gelezen IOs-apparaten op RBPEX groter zijn dan het werkelijke aantal IOs dat door de engine wordt uitgevoerd.

### <a name="data-writes"></a>Gegevens schrijven

- De primaire Compute-replica schrijft niet rechtstreeks naar pagina-servers. In plaats daarvan worden logboek records van de logboek service op de bijbehorende pagina servers opnieuw afgespeeld.
- Schrijf bewerkingen die plaatsvinden op de compute replica, worden voornamelijk naar de lokale RBPEX geschreven (file_id 0). Voor schrijf bewerkingen op logische bestanden die groter zijn dan 8 KB, met andere woorden die worden uitgevoerd met behulp van de functie voor [het verzamelen/schrijven](/sql/relational-databases/writing-pages/), wordt elke schrijf bewerking omgezet in meerdere 8 KB afzonderlijke schrijf bewerkingen naar RBPEX, aangezien de buffer groep en RBPEX altijd 8 KB-pagina's gebruiken. Als gevolg hiervan kan het aantal schrijf-IOs dat wordt gezien op RBPEX groter zijn dan het werkelijke aantal IOs dat door de engine wordt uitgevoerd.
- Voor niet-RBPEX bestanden of andere gegevens bestanden dan file_id 0 die overeenkomen met pagina servers, worden ook de schrijf bewerkingen weer gegeven. In de grootschalige-servicelaag worden deze schrijf bewerkingen gesimuleerd, omdat de reken replica's nooit rechtstreeks naar pagina servers schrijven. Het schrijven van IOPS en door voer worden uitgevoerd als ze plaatsvinden op de compute-replica, maar latentie voor gegevens bestanden met uitzonde ring van file_id 0 weerspiegelt niet de werkelijke latentie van pagina-schrijf bewerkingen.

### <a name="log-writes"></a>Schrijf bewerkingen vastleggen

- Op de primaire Compute wordt een schrijf bewerking voor het logboek verwerkt in file_id 2 van sys.dm_io_virtual_file_stats. Het schrijven van een logboek op de primaire Compute is een schrijf bewerking naar de registratie zone van het logboek.
- Logboek records worden niet op de secundaire replica met een door Voer gehard. In grootschalige wordt log asynchroon door de logboek service toegepast op de secundaire replica's. Omdat de schrijf bewerkingen in het logboek niet echt optreden op secundaire replica's, is de accounting van logboek IOs voor de secundaire replica's alleen bedoeld voor tracerings doeleinden.

## <a name="data-io-in-resource-utilization-statistics"></a>Gegevens-IO in statistieken van resource gebruik

In een niet-grootschalige-Data Base worden de gecombineerde Lees-en schrijf-IOPS voor gegevens bestanden, ten opzichte van de limiet van de [resource governance](./resource-limits-logical-server.md#resource-governance) -gegevens IOPS, gerapporteerd in [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) en [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) weer gaven in de `avg_data_io_percent` kolom. Dezelfde waarde wordt in het Azure Portal gerapporteerd als een _gegevens-io-percentage_.

In een grootschalige-data base wordt in deze kolom gerapporteerde over gegevensiops-gebruik ten opzichte van de limiet voor lokale opslag op alleen Compute replica, met name IO tegen RBPEX en `tempdb` . Een waarde van 100% in deze kolom geeft aan dat resource governance de IOPS van lokale opslag beperkt. Als dit is gekoppeld aan een prestatie probleem, stemt u de werk belasting af om minder IO te genereren of breidt u de database service doelstelling uit om de maximale [limiet](resource-limits-vcore-single-databases.md)voor _gegevens IOPS_ van de resource governance te verhogen. Voor resource governance van RBPEX Lees-en schrijf bewerkingen telt het systeem afzonderlijke IOs-KB op in plaats van een groter IOs dat kan worden uitgegeven door de SQL Server data base-engine.

Gegevens-IO voor externe pagina servers wordt niet gerapporteerd in weer gaven van resource gebruik of in de portal, maar wordt gerapporteerd in de [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF, zoals eerder is aangegeven.

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie [grootschalige vCore service tier VCore limieten](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) voor resource limieten voor een grootschalige-data base.
- Zie [query prestaties in Azure SQL database](performance-guidance.md) voor Azure SQL database afstemming van prestaties.
- Zie [prestatie bewaking met query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/) voor het afstemmen van de prestaties met behulp van query Store
- Zie [prestaties bewaken Azure SQL database met dynamische beheer weergaven](monitoring-with-dmvs.md) voor dmv-bewakings scripts