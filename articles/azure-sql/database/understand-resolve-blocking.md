---
title: Problemen met Azure SQL blocking begrijpen en oplossen
titleSuffix: Azure SQL Database
description: Een overzicht van de onderwerpen met betrekking tot Azure SQL database over blok keren en probleem oplossing.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 1/14/2020
ms.openlocfilehash: b73e72969a851428034499d447ecb162a61aa9ab
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725783"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Problemen met het Azure SQL Database blok keren begrijpen en oplossen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Doelstelling

In dit artikel wordt de blok kering in Azure SQL-data bases beschreven en wordt uitgelegd hoe u de blok kering van problemen kunt oplossen. 

In dit artikel verwijst de term verbinding naar één aangemelde sessie van de data base. Elke verbinding wordt weer gegeven als een sessie-ID (SPID) of session_id in veel Dmv's. Elk van deze SPID'S wordt vaak een proces genoemd, hoewel het geen afzonderlijke proces context is in de gebruikelijke zin. In plaats daarvan bestaat elke SPID uit de Server bronnen en gegevens structuren die nodig zijn om de aanvragen van een enkele verbinding van een bepaalde client te verwerken. Eén client toepassing kan een of meer verbindingen hebben. Vanuit het perspectief van Azure SQL Database is er geen verschil tussen meerdere verbindingen van één client toepassing op één client computer en meerdere verbindingen van meerdere client toepassingen of meerdere client computers; ze zijn atomisch. Met één verbinding kan een andere verbinding worden geblokkeerd, ongeacht de bron-client.

> [!NOTE]
> **Deze inhoud is specifiek voor Azure SQL Database.** Azure SQL Database is gebaseerd op de nieuwste stabiele versie van de Microsoft SQL Server data base-engine, waardoor de inhoud er ongeveer hetzelfde uitziet als probleemoplossings opties en hulpprogram ma's kunnen verschillen. Zie [SQL Server problemen oplossen](/troubleshoot/sql/performance/understand-resolve-blocking)voor meer informatie over blok keren in SQL Server.

## <a name="understand-blocking"></a>Meer informatie over blok keren 
 
Blok keren is een onvermijdbaar en door ontwerp kenmerken van relationele Database Management System (RDBMS) met gelijktijdigheid op basis van een vergren deling. Zoals eerder vermeld, wordt er in SQL Server geblokkeerd wanneer een sessie een vergren deling voor een specifieke resource bevat en een tweede SPID probeert een conflicterend vergrendelings type te verkrijgen voor dezelfde resource. Normaal gesp roken is het tijds bestek waarvoor de eerste SPID de resource vergrendelt, klein. Wanneer de eigenaar van de sessie de vergren deling loslaat, wordt de tweede verbinding vervolgens vrijgemaakt om een eigen vergren deling op de resource te verkrijgen en de verwerking voort te zetten. Dit is normaal gedrag en kan in de loop van een dag heel veel tijd in beslag nemen zonder merkbaar effect op de systeem prestaties.

De duur en transactie context van een query bepalen hoe lang de vergren delingen worden vastgehouden en, waardoor het effect op andere query's. Als de query niet binnen een trans actie wordt uitgevoerd (en er geen vergrendelings hints worden gebruikt), worden de vergren delingen voor SELECT-instructies alleen opgeslagen op een resource op het moment dat deze daad werkelijk wordt gelezen, niet tijdens de query. Voor INSERT-, UPDATE-en DELETE-instructies worden de vergren delingen bewaard tijdens de query, zowel voor de consistentie van de gegevens als om te voor komen dat de query wordt teruggedraaid als dat nodig is.

Voor query's die in een trans actie worden uitgevoerd, wordt de duur bepaald waarvoor de vergren delingen worden vastgehouden door het type query, het isolatie niveau van de trans actie en of vergrendelings hints worden gebruikt in de query. Raadpleeg de volgende artikelen voor een beschrijving van vergren delen, vergrendelings hints en trans actie-isolatie niveaus:

* [Vergrendeling in de database-engine](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Vergren delen en versie beheer van rijen aanpassen](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Vergrendelings modi](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Compatibiliteit vergren delen](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transacties](/sql/t-sql/language-elements/transactions-transact-sql)

Wanneer het vergren delen en blok keren van persistentie zorgt voor het punt waarop een nadelige invloed op de systeem prestaties heeft, wordt dit veroorzaakt door een van de volgende redenen:

* Een SPID bevat vergrendelde vergren delingen voor een set resources gedurende een lange periode voordat deze worden vrijgegeven. Dit type blok kering wordt na verloop van tijd opgelost, maar kan leiden tot verminderde prestaties.

* Een SPID bevat vergren delingen voor een set resources en geeft deze nooit vrij. Dit type blok kering wordt niet automatisch opgelost en voor komt de toegang tot de betrokken resources voor onbepaalde tijd.

In het eerste scenario kan de situatie zeer vloeibaar zijn, omdat verschillende SPID'S de verschillende bronnen gedurende een bepaalde periode blokkeert, waardoor er een zwevend doel wordt gemaakt. Deze situaties zijn lastig om problemen op te lossen met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) om het probleem op afzonderlijke query's te verfijnen. De tweede situatie resulteert daarentegen in een consistente status die eenvoudiger kan worden vastgesteld.

## <a name="applications-and-blocking"></a>Toepassingen en blok keren

Het kan zijn dat u zich kunt richten op het afstemmen van servers en platform problemen wanneer een probleem wordt geblokkeerd. Let echter op dat alleen aan de data base wordt voldaan, kan leiden tot een oplossing, en kan tijd en energie besparen die beter gericht zijn op het onderzoeken van de client toepassing en de query's die het verzendt. Ongeacht het niveau van zicht baarheid dat door de toepassing wordt weer gegeven met betrekking tot de database aanroepen die worden gemaakt, vereist een blokkerend probleem vaak zowel de inspectie van de exacte SQL-instructies die zijn ingediend door de toepassing als het exacte gedrag van de toepassing met betrekking tot het annuleren van query's, verbindings beheer, ophalen van alle resultaat rijen, enzovoort. Als het ontwikkel hulpprogramma geen expliciete controle over verbindings beheer toestaat, het annuleren van query's, time-out van query's, ophalen van resultaten, enzovoort, kunnen problemen met het blok keren niet worden opgelost. Dit potentieel moet nauw keurig worden onderzocht voordat u een hulp programma voor het ontwikkelen van toepassingen voor Azure SQL Database selecteert, met name voor de prestaties van gevoelige OLTP-omgevingen. 

Let op de prestaties van de data base tijdens de ontwerp-en constructie fase van de data base en toepassing. Met name moeten het Resource verbruik, het isolatie niveau en de lengte van het trans actie-pad worden geëvalueerd voor elke query. Elke query en trans actie moeten zo licht mogelijk zijn. Goede verbindings beheer discipline moet worden uitgeoefend zonder dat de toepassing kan lijken te beschikken over aanvaard bare prestaties bij een laag aantal gebruikers, maar de prestaties kunnen aanzienlijk afnemen naarmate het aantal gebruikers omhoog wordt geschaald. 

Met het juiste ontwerp van toepassingen en query's kan Azure SQL Database veel duizenden gelijktijdige gebruikers ondersteunen op één server, met weinig blok kering.

> [!Note]
> Voor meer richt lijnen voor het ontwikkelen van toepassingen raadpleegt u [verbindings problemen oplossen en andere fouten met Azure SQL database en Azure SQL Managed instance](troubleshoot-common-errors-issues.md) en [tijdelijke fout afhandeling](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Problemen oplossen blok keren

Ongeacht de blokkerings situatie waarin u zich bevindt, is de methodologie voor het oplossen van problemen met vergren deling gelijk. Met deze logische schei dingen wordt de rest van de samen stelling van dit artikel bepaald. Het concept is het vinden van de hoofd blokkering en het identificeren van de query en waarom deze wordt geblokkeerd. Zodra de problematische query is geïdentificeerd (dat wil zeggen, wat de vergren delingen voor de langste periode bewaart), is de volgende stap het analyseren en bepalen waarom de blok kering plaatsvindt. Nadat we de waarom hebben begrepen, kunnen we wijzigingen aanbrengen door de query en de trans actie opnieuw te ontwerpen.

Stappen voor het oplossen van problemen:

1. De hoofd blokkerings sessie identificeren (hoofd blok)

2. Zoek de query en trans actie die de blok kering veroorzaakt (wat voor een lange periode is vergrendeld)

3. Analyseren/begrijpen waarom de langdurige blok kering plaatsvindt

4. Probleem oplossing oplossen door query en trans actie opnieuw te ontwerpen

Laten we nu eens kijken hoe u de hoofd blokkerings sessie kunt herkennen met een juiste gegevens opname.

## <a name="gather-blocking-information"></a>Blokkerings gegevens verzamelen

Om het probleem van het blok keren van problemen op te lossen, kan een database beheerder SQL-scripts gebruiken die voortdurend de status van vergren deling en blok keren in de Azure-SQL database bewaken. Voor het verzamelen van deze gegevens zijn er in feite twee methoden. 

De eerste is het doorzoeken van dynamische Management Objects (DMOs) en het opslaan van de resultaten voor een vergelijking gedurende een bepaalde periode. Sommige objecten waarnaar in dit artikel wordt verwezen, zijn dynamische beheer weergaven (Dmv's) en sommige zijn dynamische beheer functies (DMFs). De tweede methode is om XEvents te gebruiken om te vastleggen wat er wordt uitgevoerd. 


## <a name="gather-information-from-dmvs"></a>Informatie verzamelen van Dmv's

Het verwijzen naar een Dmv's om problemen met blok keringen op te lossen, is het doel van het identificeren van de SPID (sessie-ID) aan het hoofd van de blokkerende keten en de SQL-instructie. Zoek naar dupee SPID'S die worden geblokkeerd. Als een SPID wordt geblokkeerd door een andere SPID, onderzoekt u de SPID die eigenaar is van de bron (de blokkerende SPID). Is de SPID van de eigenaar ook geblokkeerd? U kunt de keten door lopen om de hoofd blokkering te vinden. vervolgens wordt onderzocht waarom het de vergren deling houdt.

Vergeet niet om elk van deze scripts uit te voeren in de doel-Azure-SQL database.

* De opdrachten sp_who en sp_who2 zijn oudere opdrachten om alle huidige sessies weer te geven. De DMV-sys.dm_exec_sessions retourneert meer gegevens in een resultatenset die makkelijker te doorzoeken en filteren zijn. U vindt sys.dm_exec_sessions op basis van andere query's. 

* Als u al een bepaalde sessie hebt geïdentificeerd, kunt u gebruiken `DBCC INPUTBUFFER(<session_id>)` om de laatste instructie te vinden die is ingediend door een sessie. Vergelijk bare resultaten kunnen worden geretourneerd met behulp van de sys.dm_exec_input_buffer Dynamic Management Function (DMF), in een resultatenset die eenvoudiger is om op te vragen en te filteren, waardoor de session_id en de request_id. Als u bijvoorbeeld de meest recente query wilt retour neren die is ingediend door session_id 66 en request_id 0:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Raadpleeg de sys.dm_exec_requests en verwijs naar de kolom blocking_session_id. Als blocking_session_id = 0, wordt een sessie niet geblokkeerd. Tijdens sys.dm_exec_requests worden alleen aanvragen weer gegeven die momenteel worden uitgevoerd, worden alle verbindingen (actief of niet) vermeld in sys.dm_exec_sessions. Bouw op deze algemene koppeling tussen sys.dm_exec_requests en sys.dm_exec_sessions in de volgende query.

* Voer deze voorbeeld query uit om de actief uitgevoerde query's en de huidige SQL batch-tekst of invoer buffer tekst te vinden met behulp van de [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) of [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) dmv's. Als de gegevens die door het `text` veld van sys.dm_exec_sql_text zijn geretourneerd, null is, wordt de query momenteel niet uitgevoerd. In dat geval bevat het `event_info` veld van sys.dm_exec_input_buffer de laatste opdracht reeks die is door gegeven aan de SQL-engine. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Als u langlopende of niet-doorgevoerde trans acties wilt uitvoeren, gebruikt u een andere set Dmv's voor het weer geven van huidige openstaande trans acties, waaronder [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)en sys.dm_exec_sql_text. Er zijn verschillende Dmv's gekoppeld aan het bijhouden van trans acties. Zie hier meer [dmv's voor trans acties](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) . 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* Referentie [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) die zich bevindt op de thread-en BEVEILIGINGSLAAG van SQL. Hiermee wordt informatie geretourneerd over de SQL-wacht type die de aanvraag momenteel ondervindt. Net als sys.dm_exec_requests worden alleen actieve aanvragen geretourneerd door sys.dm_os_waiting_tasks. 

> [!Note]
> Zie de DMV- [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)voor veel meer informatie over wacht tijden, inclusief geaggregeerde wacht statistieken gedurende een bepaalde periode. Deze DMV retourneert geaggregeerde wait-statistieken voor de huidige data base.

* Gebruik de [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) dmv voor gedetailleerdere informatie over welke vergren delingen zijn geplaatst door query's. Deze DMV kan grote hoeveel heden gegevens op een productie SQL Server retour neren en is handig voor het vaststellen van de huidige vergrendelingen. 

Als gevolg van de INNER JOIN op sys.dm_os_waiting_tasks, beperkt de volgende query de uitvoer van sys.dm_tran_locks alleen op momenteel geblokkeerde aanvragen, hun wacht status en de vergren delingen:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Met Dmv's kunnen de query resultaten gedurende een periode gegevens punten bevatten waarmee u de blok kering van een opgegeven tijds interval kunt controleren om permanente blok keringen of trends te identificeren. 

## <a name="gather-information-from-extended-events"></a>Informatie verzamelen uit uitgebreide gebeurtenissen

Naast de bovenstaande informatie is het vaak nodig om een tracering van de activiteiten op de server vast te leggen om een probleem met het blok keren op Azure SQL Database grondig te onderzoeken. Als een sessie bijvoorbeeld meerdere instructies binnen een trans actie uitvoert, wordt alleen de laatste ingediende instructie weer gegeven. Een van de eerdere instructies kan echter de reden zijn dat de vergren delingen nog steeds worden behouden. Met een tracering kunt u alle opdrachten weer geven die worden uitgevoerd door een sessie binnen de huidige trans actie.

Er zijn twee manieren om traceringen vast te leggen in SQL Server; Extended Events (XEvents) en Profiler-traceringen. [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) is echter afgeschafte tracerings technologie die niet wordt ondersteund voor Azure SQL database. [Extended Events](/sql/relational-databases/extended-events/extended-events) is de nieuwere tracerings technologie die meer veelzijdigheid en minder impact op het waargenomen systeem mogelijk maakt en de interface is geïntegreerd in SQL Server Management Studio (SSMS). 

Raadpleeg het document waarin wordt uitgelegd hoe u de [wizard nieuwe sessie voor uitgebreide gebeurtenissen](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) in SSMS kunt gebruiken. Voor Azure SQL-data bases maakt SSMS echter een submap voor uitgebreide gebeurtenissen onder elke data base in Objectverkenner. Gebruik de wizard voor het maken van een Extended Events-sessie om deze handige gebeurtenissen vast te leggen: 

-   Categorie fouten:
    -   Schenk
    -   Error_reported  
    -   Execution_warning

-   Categorie waarschuwingen: 
    -   Missing_join_predicate

-   Uitvoering van categorie:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Vergrendelen
    -   Lock_deadlock

-   Sessie
    -   Existing_connection
    -   Aanmelden
    -   Afmelden

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Veelvoorkomende blokkerende scenario's identificeren en oplossen

Door de bovenstaande informatie te bekijken, kunt u de oorzaak van de meeste blok problemen bepalen. In de rest van dit artikel leest u hoe u deze informatie gebruikt om enkele veelvoorkomende blokkerende scenario's te identificeren en op te lossen. In deze bespreking wordt ervan uitgegaan dat u de blokkerende scripts (eerder waarnaar wordt verwezen) hebt gebruikt om informatie vast te leggen over de blokkerende SPID'S en de toepassings activiteit hebt vastgelegd met behulp van een XEvent-sessie.

## <a name="analyze-blocking-data"></a>Blokkerende gegevens analyseren 

* Bekijk de uitvoer van de Dmv's-sys.dm_exec_requests en sys.dm_exec_sessions om de hoofden van de blokkerende ketens te bepalen met behulp van blocking_these en session_id. Op deze wijze wordt duidelijk aangegeven welke aanvragen worden geblokkeerd en welke kunnen worden geblokkeerd. Bekijk de sessies die zijn geblokkeerd en blok keren. Is er een algemene of basis voor de blokkerings keten? Ze delen een gemeen schappelijke tabel en een of meer van de sessies die bij een blokkerende keten betrokken zijn, voeren een schrijf bewerking uit. 

* Bekijk de uitvoer van de Dmv's-sys.dm_exec_requests en sys.dm_exec_sessions voor informatie over de SPID'S op het hoofd van de Blokkeer keten. Zoek naar de volgende velden: 

    -    `sys.dm_exec_requests.status`  
    In deze kolom wordt de status van een bepaalde aanvraag weer gegeven. Normaal gesp roken geeft een slapende status aan dat de SPID is voltooid en wacht totdat de toepassing een andere query of batch heeft verzonden. Een uitvoer bare-of uitvoerings status geeft aan dat de SPID momenteel een query verwerkt. De volgende tabel geeft een korte beschrijving van de verschillende status waarden.

    | Status | Betekenis |
    |:-|:-|
    | Achtergrond | De SPID voert een achtergrond taak uit, zoals deadlock-detectie, logboek schrijver of controle punt. |
    | Staat | De SPID wordt momenteel niet uitgevoerd. Dit betekent meestal dat de SPID wacht op een opdracht van de toepassing. |
    | Wordt uitgevoerd | De SPID wordt momenteel uitgevoerd op een scheduler. |
    | Uitvoer bare | De SPID bevindt zich in de uitvoer bare-wachtrij van een scheduler en wacht op het ophalen van de planner tijd. |
    | Onderbroken | De SPID wacht op een resource, zoals een vergren deling of een hendel. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Dit veld geeft u het aantal openstaande trans acties in deze sessie aan. Als deze waarde groter is dan 0, bevindt de SPID zich in een openstaande trans actie en kunnen de vergren delingen worden aangehouden door een wille keurige instructie in de trans actie

    -    `sys.dm_exec_requests.open_transaction_count`  
    Dit veld vertelt u ook het aantal openstaande trans acties in deze aanvraag. Als deze waarde groter is dan 0, bevindt de SPID zich in een openstaande trans actie en kunnen de vergren delingen worden aangehouden door een wille keurige instructie in de trans actie

    -   `sys.dm_exec_requests.wait_type`, `wait_time` en `last_wait_type`  
    Als de  `sys.dm_exec_requests.wait_type`   is null is, wordt de aanvraag momenteel niet gewacht en  `last_wait_type`   wordt de waarde aangegeven die het laatst  `wait_type`   is aangetroffen. Zie sys.dm_os_wait_stats voor meer informatie over  `sys.dm_os_wait_stats` en een beschrijving van de meest voorkomende wacht typen [](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql). De  `wait_time`   waarde kan worden gebruikt om te bepalen of de aanvraag vordert. Wanneer een query voor de sys.dm_exec_requests tabel een waarde retourneert in de  `wait_time`   kolom die kleiner is dan de  `wait_time`   waarde uit een vorige query van sys.dm_exec_requests, betekent dit dat de voor gaande vergren deling is verkregen en vrijgegeven en nu op een nieuwe vergren deling wordt gewacht (ervan uitgaande dat deze niet gelijk is aan nul `wait_time` ). Dit kan worden gecontroleerd door te vergelijken van de `wait_resource`   uitvoer van sys.dm_exec_requests, waarin de resource wordt weer gegeven waarvoor de aanvraag wordt ingewisseld.

    -   `sys.dm_exec_requests.wait_resource` Dit veld geeft aan op welke resource een geblokkeerde aanvraag wacht. De volgende tabel bevat een lijst  `wait_resource`   met algemene indelingen en hun betekenis:

    | Resource | Indeling | Voorbeeld | Uitleg | 
    |:-|:-|:-|:-|
    | Tabel | DatabaseID: ObjectID: IndexID | TABBLAD: 5:261575970:1 | In dit geval is data base-ID 5 de voor beeld-data base van pubs en object-ID 261575970 is de tabel met functies en 1 is de geclusterde index. |
    | Pagina | DatabaseID: bestands-id: PageID | PAGINA: 5:1:104 | In dit geval is data base-ID 5 de pubs, bestand ID 1 het primaire gegevens bestand en pagina 104 is een pagina die deel uitmaakt van de tabel met functies. Als u de object_id waarvan de pagina deel uitmaakt, gebruikt u de Dynamic Management Function [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql), waarbij u de DatabaseID, bestands-id en PageId van de kunt opgeven `wait_resource` . | 
    | Sleutel | DatabaseID: Hobt_id (hash-waarde voor index sleutel) | SLEUTEL: 5:72057594044284928 (3300a4f361aa) | In dit geval is data base-ID 5 de pubs, Hobt_ID 72057594044284928 overeenkomt met index_id 2 voor object_id 261575970 (tabel met titels). Gebruik de catalogus weergave sys. partitions om de hobt_id aan een bepaalde index_id en object_id te koppelen. Het is niet mogelijk om de hash van de index sleutel te ontsleutelen naar een specifieke sleutel waarde. |
    | Rij | DatabaseID: bestands-id: PageID: sleuf (rij) | RID: 5:1:104:3 | In dit geval is data base-ID 5 de pubs, bestands-ID 1 is het primaire gegevens bestand, pagina 104 is een pagina die deel uitmaakt van de tabel titles en sleuf 3 de positie van de rij op de pagina aangeeft. |
    | Compileren  | DatabaseID: bestands-id: PageID: sleuf (rij) | RID: 5:1:104:3 | In dit geval is data base-ID 5 de pubs, bestands-ID 1 is het primaire gegevens bestand, pagina 104 is een pagina die deel uitmaakt van de tabel titles en sleuf 3 de positie van de rij op de pagina aangeeft. |

    -    `sys.dm_tran_active_transactions` De [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) dmv bevat gegevens over openstaande trans acties die kunnen worden gekoppeld aan andere dmv's voor een volledige afbeelding van trans acties die nog moeten worden doorgevoerd of teruggedraaid. Gebruik de volgende query om informatie te retour neren over open trans acties, die zijn gekoppeld aan andere Dmv's, inclusief [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Houd rekening met de huidige status van een trans actie, `transaction_begin_time` en andere situatie gegevens om te evalueren of het kan gaan om een bron van blok kering.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Overige kolommen

        De overige kolommen in [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) en [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) kunnen ook inzicht geven in de basis van een probleem. Het nut is afhankelijk van de omstandigheden van het probleem. U kunt bijvoorbeeld bepalen of het probleem zich alleen voordoet op bepaalde clients (hostname), op bepaalde netwerk bibliotheken (net_library), wanneer de laatste batch die is ingediend door een SPID `last_request_start_time` in sys.dm_exec_sessions, hoe lang een aanvraag werd uitgevoerd `start_time` in sys.dm_exec_requests, enzovoort.


## <a name="common-blocking-scenarios"></a>Algemene blokkerende scenario's

In de volgende tabel worden veelvoorkomende symptomen aan hun waarschijnlijke oorzaken toegewezen.  

De `wait_type` `open_transaction_count` kolommen, en bevatten `status` informatie over de gegevens die door [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)worden geretourneerd. andere kolommen kunnen worden geretourneerd door [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql). De ' oplossen? ' kolom geeft aan of de blok kering zelf wordt opgelost of dat de sessie via de opdracht moet worden afgebroken `KILL` . Zie [Kill (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql)voor meer informatie.

| Scenario | Waittype | Open_Tran | Status | Wordt omgezet? | Andere symptomen |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NIET NULL | >= 0 | uitvoer bare | Ja, wanneer de query is voltooid. | In sys.dm_exec_sessions, worden **Lees**-, **cpu_time**-en/of **memory_usage** kolommen in de loop van de tijd verg root. De duur van de query is hoog wanneer deze is voltooid. |
| 2 | NULL | \>0,3 | staat | Nee, maar SPID kan worden afgebroken. | Een attentie signaal kan worden weer gegeven in de Extended Event-sessie voor deze SPID, wat aangeeft dat er een time-out voor de query is opgetreden of dat er een annulering heeft plaatsgevonden. |
| 3 | NULL | \>= 0 | uitvoer bare | Nee. Kan pas worden opgelost nadat de client alle rijen heeft opgehaald of de verbinding heeft gesloten. SPID kan worden afgebroken, maar het kan tot 30 seconden duren. | Als open_transaction_count = 0, en de SPID bevat vergren delingen terwijl het isolatie niveau van de trans actie standaard is (Lees COMMMITTED), is dit een waarschijnlijke oorzaak. |  
| 4 | Varieert | \>= 0 | uitvoer bare | Nee. Kan pas worden omgezet wanneer de client query's annuleert of verbindingen sluit. SPID kan worden afgebroken, maar kan tot wel 30 seconden duren. | De kolom **hostname** in sys.dm_exec_sessions voor de SPID in het hoofd van een blokkerende keten is hetzelfde als een van de SPID die wordt geblokkeerd. |  
| 5 | NULL | \>0,3 | terugdraaien | Ja. | Een attentie signaal kan worden gezien in de Extended Events-sessie voor deze SPID, wat aangeeft dat er een time-out van de query is opgetreden of dat er een annulerings instructie is gegeven. |  
| 6 | NULL | \>0,3 | staat | Na. Wanneer Windows NT vaststelt dat de sessie niet langer actief is, wordt de Azure SQL Database verbinding verbroken. | De `last_request_start_time` waarde in sys.dm_exec_sessions veel eerder zijn dan de huidige tijd. |

De volgende scenario's worden in deze scenario's uitgebreid. 

1.  Blok keren dat wordt veroorzaakt door een normaal uitgevoerde query met een lange uitvoerings tijd

    **Oplossing**: de oplossing voor dit soort blokkerende problemen is het zoeken naar manieren om de query te optimaliseren. Het kan ook voor komen dat deze klasse van het blok keren van problemen een probleem met de prestaties is en dat u deze moet uitvoeren. Zie voor meer informatie over het oplossen van een specifieke trage query [problemen oplossen met trage query's op SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Zie [prestaties controleren en optimaliseren voor](/sql/relational-databases/performance/monitor-and-tune-for-performance)meer informatie. 

    Rapporten uit het [query archief](/sql/relational-databases/performance/best-practice-with-the-query-store) in SSMS zijn ook een zeer aanbevolen en waardevol hulp middel voor het identificeren van de meest kost bare query's, de meest optimale uitvoerings plannen. Bekijk ook de sectie [intelligente prestaties](intelligent-insights-overview.md) van de Azure portal voor de Azure SQL database, met inbegrip van [query Performance Insight](query-performance-insight-use.md).

    Als u een langlopende query hebt die andere gebruikers blokkeert en niet kan worden geoptimaliseerd, kunt u overwegen om deze te verplaatsen van een OLTP-omgeving naar een speciaal rapportage systeem, een [synchrone alleen-lezen replica van de data base](read-scale-out.md).

1.  Blok kering veroorzaakt door een slapende SPID die een niet-doorgevoerde trans actie heeft

    Dit type blok kering kan vaak worden geïdentificeerd door een SPID die in de slaap stand staat of in afwachting is van een opdracht, maar waarvan het transactie nest niveau ( `@@TRANCOUNT` , `open_transaction_count` van sys.dm_exec_requests) groter is dan nul. Dit kan gebeuren als de toepassing een time-out voor query's ondervindt of een annulering uitgeeft zonder het vereiste aantal TERUGDRAAI-en/of COMMIT-instructies uit te voeren. Wanneer een SPID een time-out voor query's of een annulering ontvangt, wordt de huidige query en batch beëindigd, maar wordt de trans actie niet automatisch teruggedraaid of door gegeven. De toepassing is hiervoor verantwoordelijk, omdat Azure SQL Database niet kan aannemen dat een volledige trans actie moet worden teruggedraaid als gevolg van het annuleren van één query. De time-out van de query of annuleren wordt weer gegeven als ATTENTie signaal gebeurtenis voor de SPID in de Extended Event-sessie.

    Als u een niet-vastgelegde expliciete trans actie wilt demonstreren, geeft u de volgende query op:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Voer deze query vervolgens uit in hetzelfde venster:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    De uitvoer van de tweede query geeft aan dat het nest niveau van de trans actie. Alle vergren delingen die in de trans actie zijn verkregen, blijven behouden totdat de trans actie is doorgevoerd of teruggedraaid. Als toepassingen expliciet trans acties openen en door voeren, kan een communicatie of een andere fout de sessie en de trans actie in een open status verlaten. 

    Gebruik het bovenstaande script op basis van sys.dm_tran_active_transactions om momenteel niet-doorgevoerde trans acties te identificeren.

    **Oplossingen**:

     -   Daarnaast kan deze klasse van blokkerende problemen ook een probleem met de prestaties zijn en vereisen dat u deze als zodanig moet uitvoeren. Als de uitvoerings tijd van de query kan worden verminderd, wordt de time-out van de query of de opdracht annuleren niet uitgevoerd. Het is belang rijk dat de toepassing de time-out of het annuleren van scenario's kan verwerken, maar u kunt ook profiteren van de prestaties van de query. 
     
     -    Toepassingen moeten goed te beheren niveaus voor het nesten van trans acties, of kunnen leiden tot een blokkerend probleem na het annuleren van de query op deze manier. Overweeg de volgende:  

            *    Voer in de fout afhandeling van de client toepassing een van de `IF @@TRANCOUNT > 0 ROLLBACK TRAN` volgende fouten uit, zelfs als de client toepassing niet van mening is dat er een trans actie is geopend. Controleren op openstaande trans acties is vereist omdat een opgeslagen procedure die is aangeroepen tijdens de batch, een trans actie kan hebben gestart zonder kennis van de client toepassing. Bepaalde voor waarden, zoals het annuleren van de query, voor komen dat de procedure wordt uitgevoerd in de huidige instructie, dus zelfs als de procedure logica heeft om `IF @@ERROR <> 0` de trans actie te controleren en af te breken, wordt deze terugdraai code niet in dergelijke gevallen uitgevoerd.  
            *    Als groepsgewijze verbindingen wordt gebruikt in een toepassing die de verbinding opent en een klein aantal query's uitvoert voordat de verbinding met de groep wordt hersteld, zoals een webtoepassing, kunt u het probleem mogelijk verhelpen door verbindings groepen tijdelijk uit te scha kelen totdat de client toepassing is gewijzigd om de fouten op de juiste wijze af te handelen. Door groepsgewijze verbindingen uit te scha kelen, zorgt u ervoor dat de verbinding wordt verbroken een fysieke verbreking van de Azure SQL Database verbinding, waardoor alle openstaande trans acties worden teruggedraaid.  
            *    Gebruiken `SET XACT_ABORT ON` voor de verbinding, of in een opgeslagen procedure die trans acties start en niet opschoont na een fout. Als er een runtime-fout optreedt, worden alle openstaande trans acties en de besturings elementen van de client door deze instelling afgebroken. Zie [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql)voor meer informatie.
    > [!NOTE]
    > De verbinding wordt pas opnieuw ingesteld als deze opnieuw wordt gebruikt vanuit de verbindings groep. het is dus mogelijk dat een gebruiker een trans actie kan openen en vervolgens de verbinding met de verbindings groep kan vrijgeven, maar deze kan niet langer dan een paar seconden worden gebruikt, gedurende welke tijd de trans actie geopend blijft. Als de verbinding niet opnieuw wordt gebruikt, wordt de trans actie afgebroken wanneer de verbinding wordt verbroken en uit de verbindings groep wordt verwijderd. Het is dus optimaal voor de client toepassing om trans acties in hun foutafhandelingsroutine af te breken of `SET XACT_ABORT ON` om deze mogelijke vertraging te voor komen.

    > [!CAUTION]
    > Hierna volgen `SET XACT_ABORT ON` T-SQL-instructies op een instructie die ervoor zorgt dat er geen fout wordt uitgevoerd. Dit kan van invloed zijn op de beoogde stroom van bestaande code.

1.  Blok kering veroorzaakt door een SPID waarvan de bijbehorende client toepassing niet alle resultaat rijen heeft opgehaald voor voltooiing

    Nadat een query naar de server is verzonden, moeten alle resulterende rijen onmiddellijk worden opgehaald om te worden voltooid. Als een toepassing niet alle resultaat rijen ophaalt, kunnen de vergren delingen op de tabellen worden achtergelaten en kunnen andere gebruikers worden geblokkeerd. Als u een toepassing gebruikt waarmee SQL-instructies op transparante wijze naar de server worden verzonden, moet de toepassing alle resultaat rijen ophalen. Als dat niet het geval is (en als dit niet kan worden geconfigureerd), kunt u het blokkerings probleem mogelijk niet oplossen. Om het probleem te voor komen, kunt u slecht gelaagde toepassingen beperken tot een rapportage of een beslissings-ondersteunings database.
    
    > [!NOTE]
    > Zie de [richt lijnen voor logica voor nieuwe pogingen](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) voor toepassingen die verbinding maken met Azure SQL database. 
    
    **Oplossing**: de toepassing moet opnieuw worden geschreven om alle rijen van het resultaat op te halen dat moet worden voltooid. Hiermee wordt het gebruik van [Offset en ophalen in de order by-component](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) van een query voor het uitvoeren van paginering aan de server zijde niet beschreven.

1.  Blok keren dat wordt veroorzaakt door een SPID met de status rollback

    Een query voor het wijzigen van de gegevens die buiten een door de gebruiker gedefinieerde trans actie wordt gedood of geannuleerd, wordt teruggedraaid. Dit kan ook optreden als een neven effect van de client netwerk sessie verbinding verbreken of wanneer een aanvraag is geselecteerd als slacht offer van de deadlock. Dit kan vaak worden geïdentificeerd door de uitvoer van sys.dm_exec_requests te observeren. Dit kan duiden op de TERUGDRAAI **opdracht**, en de **percent_complete kolom** kan de voortgang weer geven. 

    Dankzij de functie voor het herstellen van de [versnelde data base](../accelerated-database-recovery.md) die is geïntroduceerd in 2019, moeten de langdurige terugdraai acties zeldzaam zijn.
    
    **Oplossing**: wacht totdat de SPID klaar is met het terugdraaien van de wijzigingen die zijn aangebracht. 

    Om deze situatie te voor komen, moet u geen grote batch schrijf bewerkingen of het maken van een index of onderhouds bewerkingen uitvoeren tijdens drukke uren op OLTP-systemen. Voer, indien mogelijk, dergelijke bewerkingen uit tijdens peri Oden met een lage activiteit.

1.  Blok kering veroorzaakt door een zwevende verbinding

    Als de client toepassing fouten onderschept of het client werkstation opnieuw wordt opgestart, wordt de netwerk sessie naar de server mogelijk niet onmiddellijk geannuleerd. Vanuit het Azure SQL Database perspectief lijkt de client nog steeds aanwezig te zijn en kunnen alle vergren delingen nog steeds blijven behouden. Zie [problemen met zwevende verbindingen in SQL Server oplossen](/sql/t-sql/language-elements/kill-transact-sql#remarks)voor meer informatie. 

    **Oplossing**: als de verbinding van de client toepassing is verbroken zonder dat de resources op de juiste manier zijn opgeruimd, kunt u de SPID beëindigen met behulp van de `KILL` opdracht. De `KILL` opdracht neemt de SPID-waarde als invoer. Als u bijvoorbeeld SPID 99 wilt afsluiten, geeft u de volgende opdracht:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Zie ook

* [Bewaking en prestatieafstemming van Azure SQL Database en Azure SQL Managed Instance](/monitor-tune-overview.md)
* [Prestaties bewaken met behulp van de query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Handleiding voor transactievergrendeling en versiebeheer van rijen](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [ISOLATIE NIVEAU VAN DE TRANS ACTIE INSTELLEN](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Snelstart: Uitgebreide gebeurtenissen in SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights met behulp van AI om database prestaties te bewaken en op te lossen](intelligent-insights-overview.md)

## <a name="learn-more"></a>Meer informatie

* [Azure SQL Database: prestaties afstemmen verbeteren met automatisch afstemmen](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Verbeter de Azure SQL Database prestaties met automatisch afstemmen](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Consistente prestaties leveren met Azure SQL](/learn/modules/azure-sql-performance/)
* [Verbindings problemen en andere fouten oplossen met Azure SQL Database en Azure SQL Managed instance](troubleshoot-common-errors-issues.md)
* [Tijdelijke fout afhandeling](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)