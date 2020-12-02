---
title: Capaciteits limieten voor exclusieve SQL-groep
description: De maximum waarden die zijn toegestaan voor verschillende onderdelen van een toegewezen SQL-groep in azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7f0eff28533d8cf736d032aff61454a49bcf379e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449722"
---
# <a name="capacity-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Capaciteits limieten voor exclusieve SQL-groep in azure Synapse Analytics

De maximum waarden die zijn toegestaan voor verschillende onderdelen van een toegewezen SQL-groep in azure Synapse Analytics.

## <a name="workload-management"></a>Werklastbeheer

| Categorie | Beschrijving | Maximum |
|:--- |:--- |:--- |
| [Data Warehouse-eenheden (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maximum aantal DWU voor één exclusieve SQL-groep  | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Data Warehouse-eenheden (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standaard-DTU per server |54.000<br></br>Elke SQL-Server (bijvoorbeeld myserver.database.windows.net) heeft standaard een DTU-quotum van 54.000, waarmee Maxi maal DW5000c kan worden toegestaan. Dit quotum is gewoon een veiligheidsbeperking. U kunt het quotum verhogen door [een ondersteunings ticket te maken](sql-data-warehouse-get-started-create-support-ticket.md) en *quotum* als aanvraag type te selecteren.  U kunt uw DTU-behoeften berekenen door de 7,5 te vermenigvuldigen met het totale DWU dat nodig is, of door 9,5 te vermenigvuldigen met het totale aantal cDWU dat nodig is. Bijvoorbeeld:<br></br>DW6000 x 7,5 = 45.000 Dtu's<br></br>DW5000c x 9,5 = 47.500 Dtu's.<br></br>U kunt uw huidige DTU-verbruik weer geven met de optie SQL Server in de portal. Zowel onderbroken als niet-onderbroken databases tellen mee voor het DTU-quotum. |
| Database verbinding |Maximum aantal gelijktijdige open sessies |1024<br/><br/>Het aantal gelijktijdige open sessies varieert op basis van de geselecteerde DWU. DWU600c en hoger ondersteunen Maxi maal 1024 geopende sessies. DWU500c en lager ondersteunen een maximum aantal gelijktijdige open sessies van 512. Opmerking: er gelden limieten voor het aantal query's dat gelijktijdig kan worden uitgevoerd. Wanneer de limiet voor gelijktijdigheid wordt overschreden, wordt de aanvraag in een interne wachtrij geplaatst, waarin wordt gewacht om te worden verwerkt. |
| Database verbinding |Maxi maal geheugen voor voor bereide instructies |20 MB |
| [Werklastbeheer](resource-classes-for-workload-management.md) |Maximum aantal gelijktijdige query's |128<br/><br/>  Er worden Maxi maal 128 gelijktijdige query's uitgevoerd en er worden resterende query's in de wachtrij geplaatst.<br/><br/>Het aantal gelijktijdige query's kan afnemen wanneer gebruikers worden toegewezen aan hogere bron klassen of wanneer de instelling van de [Data Warehouse-eenheid](memory-concurrency-limits.md) wordt verlaagd. Sommige query's, zoals DMV-query's, mogen altijd worden uitgevoerd en hebben geen invloed op de limiet voor gelijktijdige query's. Zie het artikel [gelijktijdigheids limieten](memory-concurrency-limits.md) voor meer informatie over het gelijktijdig uitvoeren van query's. |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maximum GB |399 GB per DW100c. Op DWU1000c is TempDB kleiner dan 3,99 TB. |
||||

## <a name="database-objects"></a>Databaseobjecten

| Categorie | Beschrijving | Maximum |
|:--- |:--- |:--- |
| Database |Maximale grootte | Gen1:240 TB gecomprimeerd op schijf. Deze ruimte is onafhankelijk van tempdb of logboek ruimte en daarom is deze ruimte toegewezen aan permanente tabellen.  Geclusterde column Store-compressie wordt geschat op 5X.  Met deze compressie kan de Data Base worden uitgebreid tot ongeveer 1 PB wanneer alle tabellen zijn geclusterd (het standaard tabel type). <br/><br/> Gen2: onbeperkte opslag voor column Store-tabellen.  Het Rowstore-gedeelte van de data base is nog steeds beperkt tot 240 TB gecomprimeerd op schijf. |
| Tabel |Maximale grootte |Onbeperkte grootte voor column Store-tabellen. <br>60 TB voor rowstore-tabellen gecomprimeerd op schijf. |
| Tabel |Tabellen per data base | 100.000 |
| Tabel |Kolommen per tabel |1024 kolommen |
| Tabel |Bytes per kolom |Afhankelijk van het [gegevens type](sql-data-warehouse-tables-data-types.md)van de kolom. De limiet is 8000 voor char-gegevens typen, 4000 voor nvarchar of 2 GB voor de maximale gegevens typen. |
| Tabel |Bytes per rij, gedefinieerde grootte |8060 bytes<br/><br/>Het aantal bytes per rij wordt op dezelfde manier berekend als voor SQL Server met pagina compressie. Net als bij SQL Server wordt opslag voor de rij-overloop ondersteund, waardoor kolommen met een **variabele lengte** buiten rijen kunnen worden geplaatst. Wanneer variabele length-rijen uit de rij worden gepusht, wordt alleen de basis van 24 bytes in de hoofd record opgeslagen. Zie overloop gegevens van meer [dan 8 KB](https://msdn.microsoft.com/library/ms186981.aspx)voor meer informatie. |
| Tabel |Partities per tabel |15.000<br/><br/>Voor hoge prestaties raden we u aan het aantal partities dat u nodig hebt, te minimaliseren en toch uw bedrijfs vereisten te ondersteunen. Naarmate het aantal partities groeit, neemt de overhead voor DDL (Data Definition Language) en DML-bewerkingen (data manipulatie Language) toe en leidt dit tot tragere prestaties. |
| Tabel |Tekens per partitie grenswaarde. |4000 |
| Index |Niet-geclusterde indexen per tabel. |50<br/><br/>Is alleen van toepassing op rowstore-tabellen. |
| Index |Geclusterde indexen per tabel. |1<br><br/>Is van toepassing op zowel rowstore-als column Store-tabellen. |
| Index |Grootte van index sleutel. |900 bytes.<br/><br/>Is alleen van toepassing op rowstore-indexen.<br/><br/>Indexen op varchar-kolommen met een maximale grootte van meer dan 900 bytes kunnen worden gemaakt als de bestaande gegevens in de kolommen niet groter zijn dan 900 bytes wanneer de index wordt gemaakt. Later worden acties invoegen of bijwerken op de kolommen die ervoor zorgen dat de totale grootte groter is dan 900 bytes. |
| Index |Sleutel kolommen per index. |16<br/><br/>Is alleen van toepassing op rowstore-indexen. Geclusterde column Store-indexen bevatten alle kolommen. |
| statistieken |De grootte van de gecombineerde kolom waarden. |900 bytes. |
| statistieken |Kolommen per statistiek object. |32 |
| statistieken |Statistieken gemaakt voor kolommen per tabel. |30.000 |
| Opgeslagen procedures |Maximum aantal geneste niveaus. |8 |
| Weergave |Kolommen per weer gave |1\.024 |
||||

## <a name="loads"></a>Laden

| Categorie | Beschrijving | Maximum |
|:--- |:--- |:--- |
| Poly base belastingen |MB per rij |1<br/><br/>Poly base laadt rijen die kleiner zijn dan 1 MB. Het laden van LOB-gegevens typen in tabellen met een geclusterde column store-index (CCI) wordt niet ondersteund.<br/> |
|Poly base belastingen|Totaal aantal bestanden|1.000.000<br/><br/>Poly base ladingen mogen niet meer dan 1M bestanden overschrijden. De volgende fout kan optreden: de **bewerking is mislukt omdat het aantal gesplitste items groter is dan de bovengrens van 1000000**.|

## <a name="queries"></a>Query's

| Categorie | Beschrijving | Maximum |
|:--- |:--- |:--- |
| Query’s uitvoeren |Query's in de wachtrij voor gebruikers tabellen. |1000 |
| Query’s uitvoeren |Gelijktijdige query's op systeem weergaven. |100 |
| Query’s uitvoeren |Query's in de wachtrij op systeem weergaven |1000 |
| Query’s uitvoeren |Maximum aantal para meters |2098 |
| Batch |Maximumgrootte |65.536 * 4096 |
| Resultaten selecteren |Kolommen per rij |4096<br/><br/>U kunt nooit meer dan 4096 kolommen per rij in het selectie resultaat hebben. Er is geen garantie dat u altijd 4096 kunt hebben. Als het query plan een tijdelijke tabel vereist, kunnen de 1024-kolommen per tabel maximum worden toegepast. |
| SELECT |Geneste subquery's |32<br/><br/>U kunt nooit meer dan 32 geneste subquery's in een SELECT-instructie hebben. Er is geen garantie dat u altijd 32 kunt hebben. Met een koppeling kan bijvoorbeeld een subquery worden geïntroduceerd in het query plan. Het aantal subquery's kan ook worden beperkt door het beschik bare geheugen. |
| SELECT |Kolommen per samen voeging |1024 kolommen<br/><br/>In de koppeling kunt u nooit meer dan 1024 kolommen hebben. Er is geen garantie dat u altijd 1024 kunt hebben. Als voor het deelname plan een tijdelijke tabel met meer kolommen is vereist dan het resultaat van de deelname, geldt de limiet van 1024 voor de tijdelijke tabel. |
| SELECT |Bytes per kolom op basis van een groep. |8060<br/><br/>De kolommen in de GROUP BY-component kunnen Maxi maal 8060 bytes bevatten. |
| SELECT |Aantal bytes per GESORTEERDe kolom |8060 bytes<br/><br/>De kolommen in de ORDER BY-component kunnen Maxi maal 8060 bytes bevatten |
| Id's per instructie |Aantal id's waarnaar wordt verwezen |65.535<br/><br/> Het aantal id's dat kan worden opgenomen in één expressie van een query is beperkt. Het overschrijden van dit getal resulteert in SQL Server fout 8632. Zie [interne fout: er is een limiet voor de expressie Services bereikt](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)voor meer informatie. |
| Letterlijke tekenreeksen | Aantal letterlijke teken reeksen in een instructie | 20.000 <br/><br/>Het aantal teken reeks constanten in één expressie van een query is beperkt. Het overschrijden van dit getal resulteert in SQL Server fout 8632.|
||||

## <a name="metadata"></a>Metagegevens

| Systeem weergave | Maximum aantal rijen |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10.000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Totaal aantal DMS-werk rollen voor de meest recente 1000 SQL-aanvragen. |
| sys.dm_pdw_errors |10.000 |
| sys.dm_pdw_exec_requests |10.000 |
| sys.dm_pdw_exec_sessions |10.000 |
| sys.dm_pdw_request_steps |Totaal aantal stappen voor de meest recente 1000 SQL-aanvragen die zijn opgeslagen in sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10.000 |
| sys.dm_pdw_sql_requests |De meest recente 1000 SQL-aanvragen die zijn opgeslagen in sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie het [blad Cheat](cheat-sheet.md)voor aanbevelingen voor het gebruik van Azure Synapse.
