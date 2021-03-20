---
title: Server parameters – grootschalige (Citus)-Azure Database for PostgreSQL
description: Para meters in de grootschalige (Citus) SQL-API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336238"
---
# <a name="server-parameters"></a>Serverparameters

Er zijn verschillende server parameters die van invloed zijn op het gedrag van grootschalige (Citus), zowel van standaard PostgreSQL, en specifiek voor grootschalige (Citus).
Deze para meters kunnen worden ingesteld in de Azure Portal voor een Citus-Server groep (grootschalige). Kies in de categorie **instellingen** de para meters van **worker-knoop punten** of **para meters van coördinator knooppunt**. Met deze pagina's kunt u para meters instellen voor alle worker-knoop punten of alleen voor het coördinator knooppunt.

## <a name="hyperscale-citus-parameters"></a>Citus-para meters (grootschalige)

> [!NOTE]
>
> Grootschalige (Citus)-Server groepen waarop oudere versies van de Citus-engine worden uitgevoerd, bieden mogelijk niet alle onderstaande para meters.

### <a name="general-configuration"></a>Algemene configuratie

#### <a name="citususe_secondary_nodes-enum"></a>Citus. Gebruik \_ secundaire \_ knoop punten (enum)

Hiermee stelt u het beleid dat moet worden gebruikt bij het kiezen van knoop punten voor SELECT-query's. Als deze instelling is ingesteld op ' Always ', worden door de planner alleen een query voor de knoop punten die zijn gemarkeerd als ' secundair ' noderole in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

De ondersteunde waarden voor deze Enum zijn:

-   **nooit:** (standaard) alle Lees bewerkingen worden uitgevoerd op primaire knoop punten.
-   **altijd:** Er worden in plaats daarvan lees bewerkingen uitgevoerd voor secundaire knoop punten en instructies INSERT/update zijn uitgeschakeld.

#### <a name="cituscluster_name-text"></a>Citus. cluster \_ naam (tekst)

Informeert de Planner van het coördinator knooppunt welk cluster it coördineert. Wanneer \_ de cluster naam is ingesteld, worden in de planner alleen werk knooppunten in dat cluster opgevraagd.

#### <a name="citusenable_version_checks-boolean"></a>Citus. \_ versie \_ controles inschakelen (Booleaanse waarde)

Voor het upgraden van de grootschalige-versie (Citus) moet de server opnieuw worden opgestart (om de nieuwe gedeelde bibliotheek op te halen), gevolgd door de opdracht ALTER EXTENSION UPDATE.  Als de uitvoering van beide stappen mislukt, kan dit leiden tot fouten of crashes.
Grootschalige (Citus) valideert daarom de versie van de code en die van de uitbrei ding die overeenkomt, en er wordt een fout opgetreden als deze niet \' .

Deze waarde is standaard ingesteld op waar en is van kracht op de coördinator. In zeldzame gevallen kan het nodig zijn dat voor complexe upgrades deze para meter moet worden ingesteld op False, waardoor de controle wordt uitgeschakeld.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>\_Distributed \_ deadlock-detectie Citus. log \_ (Booleaans)

Hiermee wordt aangegeven of gedistribueerde verwerking van deadlock-detectie moet worden vastgelegd in het server logboek. De standaard waarde is False.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>Citus. distributed \_ deadlock \_ detectie \_ factor (drijvende komma)

Hiermee stelt u de tijd in die moet worden gewacht voordat wordt gecontroleerd op gedistribueerde deadlocks. In het bijzonder is de tijd die moet worden gewacht, de waarde wordt vermenigvuldigd met de \' [ \_ time-](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) outinstelling postgresql s. De standaardwaarde is `2`. De waarde van de `-1` detectie van gedistribueerde deadlock wordt uitgeschakeld.

#### <a name="citusnode_connection_timeout-integer"></a>\_time-out voor verbinding met Citus. node \_ (geheel getal)

De `citus.node_connection_timeout` GUC stelt de maximale duur (in milliseconden) in die moet worden gewacht op het tot stand brengen van de verbinding. Grootschalige (Citus) veroorzaakt een fout als de time-out is verstreken voordat ten minste één werk verbinding tot stand is gebracht. Deze GUC is van invloed op verbindingen van de coördinator voor werk nemers en werk rollen.

-   Standaard: vijf seconden
-   Minimum: 10 milliseconden
-   Maximum: één uur

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Query statistieken

#### <a name="citusstat_statements_purge_interval-integer"></a>Citus. stat- \_ instructies \_ opschonen \_ interval (geheel getal)

Hiermee stelt u de frequentie in waarmee de onderhouds-daemon records verwijdert van [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) die niet overeenkomen in `pg_stat_statements` . Met deze configuratie waarde wordt het tijds interval tussen leegmaak handelingen in seconden ingesteld, met een standaard waarde van 10. Met de waarde 0 worden de leegmaak handelingen uitgeschakeld.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Deze para meter is van kracht op de coördinator en kan worden gewijzigd tijdens runtime.

### <a name="data-loading"></a>Gegevens laden

#### <a name="citusmulti_shard_commit_protocol-enum"></a>Citus. multi \_ Shard \_ commit \_ Protocol (enum)

Hiermee stelt u het doorvoer protocol dat moet worden gebruikt bij het uitvoeren van een kopie in een gedistribueerde hash-tabel. Op elke afzonderlijke Shard-plaatsing wordt de kopie uitgevoerd in een transactie blok om ervoor te zorgen dat er geen gegevens worden opgenomen als er een fout optreedt tijdens de kopie. Er is echter een specifieke fout opgetreden waarbij de kopie op alle plaatsingen slaagt, maar er treedt een (hardware)-fout op voordat alle trans acties worden doorgevoerd. Deze para meter kan worden gebruikt om gegevens verlies in dat geval te voor komen door te kiezen tussen de volgende doorvoer protocollen:

-   **2pc:** (standaard) de trans acties waarbij een kopie wordt gemaakt op de Shard-plaatsingen worden eerst voor bereid met behulp van postgresql \' s [door voeren in twee fasen](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) en vervolgens worden doorgevoerd. Mislukte door voeringen kunnen hand matig worden hersteld of worden afgebroken met respectievelijk voor BEREIDing of terugdraaien.
    Wanneer u 2PC gebruikt, moeten de [maximale \_ voor bereide \_ trans acties](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) worden verhoogd voor alle werk nemers, meestal tot dezelfde waarde als het maximum aantal \_ verbindingen.
-   **1pc:** De trans acties waarbij een kopie wordt uitgevoerd op de Shard plaatsingen, worden in één ronde doorgevoerd. Gegevens kunnen verloren gaan als het door voeren mislukt nadat het kopiëren is geslaagd op alle plaatsingen (zeldzaam).

#### <a name="citusshard_replication_factor-integer"></a>Citus. Shard \_ \_ -replicatie factor (geheel getal)

Hiermee stelt u de replicatie factor voor Shards, het aantal knoop punten waarop Shards worden geplaatst en de standaard waarde is 1. Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator. De ideale waarde voor deze para meter is afhankelijk van de grootte van het cluster en de frequentie van knoop punten.  U kunt deze replicatie factor bijvoorbeeld verhogen als u grote clusters uitvoert en knooppunt fouten op een meer frequente wijze bekijkt.

#### <a name="citusshard_count-integer"></a>Citus. Shard \_ Count (geheel getal)

Hiermee stelt u het aantal Shard voor hash-gepartitioneerde tabellen en de standaard waarden in op 32.  Deze waarde wordt gebruikt door de [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF bij het maken van hash-gepartitioneerde tabellen. Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

#### <a name="citusshard_max_size-integer"></a>Citus. Shard \_ maximum \_ grootte (geheel getal)

Hiermee stelt u de maximale grootte in waarmee een Shard wordt verg root voordat deze wordt gesplitst en wordt standaard ingesteld op 1 GB. Wanneer de grootte van het bron bestand \' (dat wordt gebruikt voor fase ring) voor één Shard de configuratie waarde overschrijdt, zorgt de data base ervoor dat er een nieuwe Shard wordt gemaakt. Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

### <a name="planner-configuration"></a>Planner configuratie

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>\_ \_ aantal opgehaalde rijen van Citus. Limit \_ -component \_ (geheel getal)

Hiermee stelt u het aantal rijen dat per taak moet worden opgehaald voor het optimaliseren van de component Limit.
In sommige gevallen moet u query's met limiet componenten mogelijk alle rijen van elke taak moeten ophalen om resultaten te genereren. In die gevallen en waar een benadering zinvolle resultaten oplevert, stelt deze configuratie waarde het aantal rijen in dat moet worden opgehaald uit elke Shard. Limiet benaderingen zijn standaard uitgeschakeld en deze para meter is ingesteld op-1. Deze waarde kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>Citus. Count \_ DISTINCT- \_ fout \_ frequentie (drijvende komma)

Met grootschalige (Citus) kan Count (DISTINCT) worden berekend met behulp van de postgresql-HLL-extensie. Met deze configuratie vermelding wordt het gewenste fout aantal ingesteld bij het berekenen van het aantal (DISTINCT). 0,0: de standaard instelling is het uitschakelen van benaderingen voor aantal (DISTINCT); en 1,0 biedt geen garanties over de nauw keurigheid van de resultaten. U wordt aangeraden deze para meter in te stellen op 0,005 voor de beste resultaten. Deze waarde kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

#### <a name="citustask_assignment_policy-enum"></a>Citus. taak \_ toewijzings \_ beleid (enum)

> [!NOTE]
> Deze GUC is alleen van toepassing als [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) groter is dan één, of voor query's op [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Hiermee stelt u het beleid dat moet worden gebruikt voor het toewijzen van taken aan werk nemers. De coördinator wijst taken toe aan werk nemers op basis van Shard-locaties. Met deze configuratie waarde geeft u het beleid op dat moet worden gebruikt bij het maken van deze toewijzingen.
Op dit moment zijn er drie mogelijke taak toewijzings beleidsregels die kunnen worden gebruikt.

-   **Greedy:** Het Greedy-beleid is de standaard instelling en is erop gericht om taken gelijkmatig te verdelen over de werk nemers.
-   **Round-Robin:** Het Round Robin-beleid wijst taken toe aan werk rollen in een Round-Robin mode-afwisseling tussen verschillende replica's. Met dit beleid wordt het cluster gebruik verbeterd wanneer het aantal Shard voor een tabel laag is ten opzichte van het aantal werk rollen.
-   **eerste replica:** Met het beleid voor de eerste replica worden taken toegewezen op basis van de invoeg volgorde van plaatsingen (replica's) voor de Shards. Met andere woorden, de fragment query voor een Shard wordt toegewezen aan de werk nemer die de eerste replica van die Shard heeft.
    Met deze methode kunt u sterke garanties hebben over welke Shards worden gebruikt op welke knoop punten (dat wil zeggen sterker locatie van het geheugen).

Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

### <a name="intermediate-data-transfer"></a>Tussenliggende Gegevensoverdracht

#### <a name="citusbinary_worker_copy_format-boolean"></a>Citus. binary \_ worker \_ \_ -indeling (Booleaans)

Gebruik de indeling binaire kopie om tussenliggende gegevens tussen werk nemers over te dragen.
Tijdens grote tabel samenvoegingen kan grootschalige (Citus) de gegevens tussen verschillende werk nemers dynamisch opnieuw partitioneren en in een wille keurige volg orde plaatsen. Standaard worden deze gegevens overgebracht in een tekst indeling. Door deze para meter in te scha kelen, geeft u de data base de binaire serialisatie-indeling van PostgreSQL gebruiken om deze gegevens over te dragen. Deze para meter is van toepassing op de werk nemers en moet worden gewijzigd in het bestand postgresql. conf. Na het bewerken van het configuratie bestand kunnen gebruikers een SIGHUP-signaal verzenden of de server opnieuw opstarten om deze wijziging van kracht te laten worden.

#### <a name="citusbinary_master_copy_format-boolean"></a>indeling van kopiëren van Citus. binary \_ \_ \_ -Master (Booleaanse waarde)

Gebruik de indeling binaire kopie om gegevens over te dragen tussen coördinator en werk nemers. Bij het uitvoeren van gedistribueerde query's, sturen de werk nemers hun tussenliggende resultaten naar de coördinator voor definitieve aggregatie. Standaard worden deze gegevens overgebracht in een tekst indeling. Door deze para meter in te scha kelen, geeft u de data base de binaire serialisatie-indeling van PostgreSQL gebruiken om deze gegevens over te dragen.
Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

#### <a name="citusmax_intermediate_result_size-integer"></a>Citus. maximum \_ tussenliggende \_ resultaat \_ grootte (geheel getal)

De maximale grootte in KB van tussenliggende resultaten voor Cte's die niet kunnen worden gepusht naar worker-knoop punten voor uitvoering en voor complexe subquery's. De standaard instelling is 1 GB en de waarde-1 houdt in dat er geen limiet is.
Query's die de limiet overschrijden, worden geannuleerd en genereren een fout bericht.

### <a name="ddl"></a>LANGUAGE

#### <a name="citusenable_ddl_propagation-boolean"></a>Citus. \_ DDL \_ -doorgifte inschakelen (Booleaans)

Hiermee geeft u op of DDL-wijzigingen van de coördinator automatisch moeten worden door gegeven aan alle werk nemers. De standaardwaarde is waar. Omdat voor sommige schema wijzigingen een Access exclusieve vergren deling op tabellen is vereist en omdat de automatische doorgifte van toepassing is op alle werk nemers, kan deze een grootschalige-cluster (Citus) tijdelijk minder reageren. U kunt ervoor kiezen om deze instelling uit te scha kelen en wijzigingen hand matig door te voeren.

### <a name="executor-configuration"></a>Configuratie van de uitvoerder

#### <a name="general"></a>Algemeen

##### <a name="citusall_modifications_commutative"></a>Citus. alle \_ wijzigingen \_ Commutative

Grootschalige (Citus) dwingt commutativity-regels af en verwerft de juiste vergren delingen voor wijzigings bewerkingen om de juiste werking te garanderen. Er wordt bijvoorbeeld aangenomen dat een instructie INSERT werkt met een andere INSERT-instructie, maar niet met een instructie UPDATE of DELETE. Ook wordt ervan uitgegaan dat een UPDATE-of DELETE-instructie niet werkt met een andere UPDATE-of DELETE-instructie. Deze voorzorgsmaatregel houdt in dat UPDATEs en verwijderingen grootschalige (Citus) nodig hebben om sterkere vergren delingen te verkrijgen.

Als u UPDATE-instructies hebt die zijn Commutative met uw toevoegingen of andere UPDATEs, kunt u deze commutativity-hypo Thesen versoepelen door deze para meter in te stellen op True. Als deze para meter is ingesteld op True, worden alle opdrachten beschouwd als Commutative en claimt u een gedeelde vergren deling, waarmee de algehele door Voer kan worden verbeterd. Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

##### <a name="citusremote_task_check_interval-integer"></a>controle-interval voor Citus. externe \_ taak \_ \_ (geheel getal)

Hiermee stelt u de frequentie in waarmee grootschalige (Citus) controleert op status van taken die worden beheerd door de taak tracker-uitvoerder. De standaard waarde is 10 MS. De coördinator wijst taken toe aan werk nemers en controleert deze vervolgens regel matig op de voortgang van elke taak \' . Met deze configuratie waarde wordt het tijds interval tussen twee daaropvolgende controles ingesteld. Deze para meter is van kracht op de coördinator en kan worden ingesteld tijdens runtime.

##### <a name="citustask_executor_type-enum"></a>Citus. taak \_ \_ uitvoerings type (enum)

Grootschalige (Citus) heeft drie Voer typen voor het uitvoeren van gedistribueerde SELECT-query's.  U kunt de gewenste uitvoerder selecteren door deze configuratie parameter in te stellen. De geaccepteerde waarden voor deze para meter zijn:

-   **adaptief:** De standaard waarde. Het is optimaal voor snelle reacties op query's met aggregaties en naast elkaar verbonden samen voegingen over meerdere Shards.
-   **taak-vastleggen:** De taak voor het bijhouden van taken is goed geschikt voor langdurige, complexe query's waarvoor het opnieuw instellen van gegevens op verschillende werk knooppunten en efficiënt resource beheer nodig is.
-   **real-time:** (afgeschaft) is een vergelijkbaar doel als de adaptieve uitvoerder, maar is minder flexibel en kan meer verbindings druk veroorzaken op werk knooppunten.

Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>Citus. \_ query-logboek niveau voor meerdere taken \_ \_ \_ (enum) {#multi_task_logging}

Hiermee stelt u een logboek niveau in voor elke query die meer dan een taak genereert (dat wil zeggen, die meer dan één Shard oplevert). Logboek registratie is handig tijdens het migreren van een multi tenant-toepassing, omdat u kunt kiezen voor een fout of waarschuwing voor dergelijke query's om ze te zoeken en een Tenant \_ -id filter toe te voegen. Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator. De standaard waarde voor deze para meter is \' uitgeschakeld \' .

De ondersteunde waarden voor deze Enum zijn:

-   **uit:** Schakel de registratie van query's waarmee meerdere taken worden gegenereerd uit (dat wil zeggen, meerdere Shards)
-   **fout opsporing:** De instructie logs op de ernst van de fout opsporing.
-   **logboek:** De instructie logs op het niveau van het logboek bestand. De logboek regel bevat de SQL-query die is uitgevoerd.
-   **kennisgeving:** De instructie logs op Ernst niveau van de melding.
-   **Waarschuwing:** De logboeken-instructie op waarschuwing Ernst niveau.
-   **fout:** De instructie logs op het niveau van de fout code.

Het kan nuttig zijn om te gebruiken `error` tijdens de ontwikkelings test en een lager logboek niveau, zoals `log` tijdens de werkelijke productie-implementatie.
`log`Als u kiest, worden query's voor meerdere taken weer gegeven in de database logboeken met de query zelf weer gegeven na \" instructie.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>Citus. \_ Repartition- \_ samen voegingen inschakelen (Booleaans)

Normaal gesp roken wordt geprobeerd om opnieuw Partition samen te voegen met de adaptieve uitvoerder, mislukt met een fout bericht.  Als deze optie is ingesteld `citus.enable_repartition_joins` op True, kan grootschalige (Citus) tijdelijk overschakelen naar de uitvoerder voor taak tracering om de koppeling uit te voeren.  De standaardwaarde is false.

#### <a name="task-tracker-executor-configuration"></a>Configuratie van de taak bijhouden-uitvoerder

##### <a name="citustask_tracker_delay-integer"></a>Citus. taak \_ vastleggen \_ vertraging (geheel getal)

Met deze para meter wordt de slaap tijd van taken bijhouden tussen taak beheer en de standaard waarde 200 MS. Het proces van taak tracering wordt regel matig geactiveerd, gaat over alle taken die hieraan zijn toegewezen, en plant en voert deze taken uit.  Vervolgens wordt de slaap stand geactiveerd voor een periode voordat deze taken opnieuw worden uitgevoerd.
Deze configuratie waarde bepaalt de lengte van die slapende periode. Deze para meter is van toepassing op de werk nemers en moet worden gewijzigd in het bestand postgresql. conf. Na het bewerken van het configuratie bestand kunnen gebruikers een SIGHUP-signaal verzenden of de server opnieuw opstarten om de wijziging door te voeren.

Deze para meter kan worden verkleind om de vertraging te verkorten die wordt veroorzaakt door de taak tracering uit te voeren door de tijd tussen het beheer af te korten.
Het verminderen van de vertraging is handig in gevallen waarin de Shard query's korter zijn en daarom hun status regel matig bijwerken.

##### <a name="citusmax_assign_task_batch_size-integer"></a>Citus. Max \_ . \_ taak \_ batch \_ grootte (geheel getal) toewijzen

De taak tracker-uitvoerder van de coördinator wijst taken in batches synchroon toe aan de daemon van de werk rollen. Met deze para meter wordt het maximum aantal taken ingesteld dat in één batch kan worden toegewezen. Het kiezen van een grotere Batch grootte maakt de taak toewijzing sneller. Als het aantal werk rollen echter groot is, kan het langer duren voordat alle werk nemers taken hebben ontvangen.  Deze para meter kan tijdens runtime worden ingesteld en is van kracht op de coördinator.

##### <a name="citusmax_running_tasks_per_node-integer"></a>Citus. maximum aantal \_ actieve \_ taken \_ per \_ knoop punt (geheel getal)

Het proces voor taak tracering plant en voert de taken die hieraan zijn toegewezen, uit. Met deze configuratie waarde stelt u het maximum aantal taken in dat gelijktijdig op één knoop punt kan worden uitgevoerd en wordt standaard ingesteld op 8.

De limiet zorgt ervoor dat u niet \' veel taken tegelijk op dezelfde tijd hoeft te doen en helpt bij het voor komen van schijf-I/O-conflicten. Als uw query's worden verwerkt vanuit het geheugen of Ssd's, kunt u het maximum aantal \_ actieve \_ taken \_ per \_ knoop punt verhogen zonder dat hiervoor veel aandacht wordt gevraagd.

##### <a name="cituspartition_buffer_size-integer"></a>Citus. partition \_ buffer \_ grootte (geheel getal)

Hiermee stelt u de buffer grootte in die moet worden gebruikt voor partitie bewerkingen en de standaard waarde is 8 MB.
Met grootschalige (Citus) kunnen tabel gegevens opnieuw worden gepartitioneerd naar meerdere bestanden wanneer twee grote tabellen worden toegevoegd. Nadat deze partitie buffer vol is, worden de gepartitioneerde gegevens in de bestanden op de schijf leeg gemaakt.  Deze configuratie vermelding kan tijdens runtime worden ingesteld en is van kracht op de werk nemers.

#### <a name="explain-output"></a>Uitleg van uitvoer

##### <a name="citusexplain_all_tasks-boolean"></a>Citus. \_ alle \_ taken uitleggen (Booleaanse waarde)

Grootschalige (Citus) toont standaard de uitvoer van één wille keurige taak bij het uitvoeren van een [uitleg](http://www.postgresql.org/docs/current/static/sql-explain.html) over een gedistribueerde query. In de meeste gevallen lijkt de uitvoer van de uitleg op verschillende taken. Af en toe worden sommige taken anders gepland of hebben ze veel hogere uitvoerings tijden. In dergelijke gevallen kan het handig zijn om deze para meter in te scha kelen, waarna de uitleg uitvoer alle taken bevat. Als u alle taken uitlegt, kan dit ertoe leiden dat de uitleg langer duurt.

## <a name="postgresql-parameters"></a>PostgreSQL-para meters

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) : Hiermee stelt u de weergave notatie voor datum-en tijd waarden in
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) : Hiermee stelt u de weergave notatie voor interval waarden in
* [Tijd zone: Hiermee](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) stelt u de tijdzone voor het weer geven en interpreteren van tijds tempels in
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) : Hiermee stelt u de naam van de toepassing in die moet worden gerapporteerd in statistieken en Logboeken
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) -invoer van null-elementen in matrices inschakelen
* [autovacuüm](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) : Hiermee wordt het subproces voor de autovacuüm gestart
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) -aantal tuple-invoeg acties,-updates of-verwijderingen vóór de analyse als een fractie van reltuples
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) -minimum aantal tuple-invoeg acties,-updates of-verwijderingen vóór de analyse
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) -time-to-slaap stand tussen autoonderdruk-uitvoeringen
* uitstel van [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) onderdruk in milliseconden, voor autovacuüm
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) vacuüm beschik bare hoeveelheid voor Napping, voor autovacuüm
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) -aantal tuple-updates of verwijderingen vóór vacuüm als een fractie van reltuples
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) -minimum aantal tuple-updates of verwijderingen vóór vacuüm
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) : Hiermee stelt u de maximale hoeveelheid geheugen in die door elk autovacuüm werk proces moet worden gebruikt
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) -aantal pagina's waarna eerder uitgevoerde schrijf bewerkingen zijn leeg gemaakt op de schijf
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) : bepaalt of " \' " is toegestaan in letterlijke teken reeksen
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) -achtergrond van schrijver in slaap stand tussen rondes
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) -aantal pagina's waarna eerder uitgevoerde schrijf bewerkingen zijn leeg gemaakt op de schijf
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) -achtergrond schrijver maximum aantal LRU pagina's dat per ronde moet worden leeg gemaakt
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) -veelvoud van het gemiddelde buffer gebruik per afronding
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) : de uitvoer indeling voor bytea instellen
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) -controleert functie teksten tijdens het maken van een functie
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) -tijd die is besteed aan het leegmaken van gewijzigde buffers tijdens het controle punt, als Fractie van het interval van het controle punt
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) : de maximale tijd instellen tussen de automatische wal-controle punten
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) : Hiermee wordt een waarschuwing ingeschakeld als de controlepunt segmenten vaker worden gevuld dan
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) : Hiermee stelt u de teken reeks codering van de client in
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) : Hiermee stelt u de bericht niveaus in die naar de client worden verzonden
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) : Hiermee stelt u de vertraging in micro seconden tussen het door voeren van trans acties en het leegmaken van wal naar schijf in.
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) : Hiermee stelt u de mini maal gelijktijdige open trans acties in voordat u commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) : Hiermee stelt u de planner in staat om beperkingen te gebruiken voor het optimaliseren van query's
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) : stelt de schatting van de kosten voor de verwerking van elke index vermelding tijdens een index scan in
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) : stelt de schatting van de kosten voor de verwerking van elke operator of functie aanroep in
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) : stelt de schatting van de kosten voor de verwerking van elke tupel (rij) in.
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) : stelt de schatting van de planner in voor de Fractie van de rijen van een cursor die wordt opgehaald
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) : Hiermee stelt u de hoeveelheid tijd in milliseconden op die moet worden gewacht op een vergren deling voordat wordt gecontroleerd op deadlock
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) -inspringingen parseren en plannings structuur weer geven
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) : Hiermee wordt de parserings structuur van elke query geregistreerd
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -het uitvoerings plan van elke query vastleggen
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -registreert de herschreven parseringsstructuur van elke query
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) : het standaard statistieken doel instellen
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) : Hiermee stelt u de standaard tabel ruimte in voor het maken van tabellen en indexen in
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) : Hiermee stelt u de standaard configuratie voor tekst zoeken in
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) : Hiermee stelt u de standaard status van nieuwe trans acties in
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) : het transactie isolatie niveau van elke nieuwe trans actie instellen
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) : Hiermee stelt u de standaard alleen-lezen status van nieuwe trans acties in
* default_with_oids-maakt standaard nieuwe tabellen met Oid's
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) : stelt de veronderstelling van de planner in voor de grootte van de schijf cache
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) : Hiermee wordt het gebruik van bitmap-scan plannen door de planner ingeschakeld
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) : Hiermee stelt u het gebruik in van de planner voor het verzamelen van samenvoeg plannen
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) : Hiermee wordt het gebruik van gehashte aggregatie plannen door de planner ingeschakeld
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) : Hiermee wordt het gebruik van hash join plannen door de planner ingeschakeld
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) : Hiermee wordt het gebruik van alleen-scan abonnementen voor de planner ingeschakeld
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) : Hiermee maakt u het gebruik van de planningen voor index-scans mogelijk
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) : Hiermee wordt het gebruik van materialisatie in de planner ingeschakeld
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) : Hiermee stelt u het gebruik in van samenvoeg abonnementen van de planner
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) : Hiermee wordt het gebruik van geneste lus-deelname plannen door de planner ingeschakeld
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) : het gebruik van sequentiële scan plannen door de Planner inschakelen
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) : Hiermee stelt u het gebruik van expliciete Sorteer stappen voor de planner in
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) : Hiermee wordt het gebruik van TID-scan plannen van de planner ingeschakeld
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) : er wordt een waarschuwing gegeven over back slash-escape tekens in normale letterlijke teken reeksen
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) -de sessie op een wille keurige fout beëindigen
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) : Hiermee stelt u het aantal cijfers dat wordt weer gegeven voor drijvende-komma waarden
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) -dwingt het gebruik van parallelle query faciliteiten af
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) : Hiermee stelt u de grootte van de lijst in waarboven de subquery's niet worden samengevouwen
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) -schakelt de optimalisatie van genetische query's in
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo: effort wordt gebruikt om de standaard waarde voor andere geqo-para meters in te stellen
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo: aantal herhalingen van de algoritme
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo: aantal personen in de populatie
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo: Zaai zaad voor wille keurig pad selecteren
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo: selectief druk binnen de populatie
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) : Hiermee stelt u de drempel waarde in van items waarboven geqo wordt gebruikt
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) : Hiermee stelt u het Maxi maal toegestane resultaat voor exacte zoek acties op eginnen
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) : Hiermee stelt u de maximale grootte in van de in behandeling zijnde lijst voor eginnen-index
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) : de Maxi maal toegestane duur van elke trans actie in Stationairen instellen
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) : Hiermee stelt u de grootte van de lijst in waarboven de samenvoegings constructies niet worden afgevlakt
* [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) : Hiermee stelt u de land instelling voor het opmaken van monetaire bedragen in
* [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) : Hiermee stelt u de land instelling in voor de notatie van getallen
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) -achterwaartse compatibiliteits modus inschakelen voor bevoegdheids controles voor grote objecten
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) : Hiermee stelt u de Maxi maal toegestane duur (in milliseconden) van de wacht tijd voor een vergren deling in. 0 schakelt u dit uit
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) : Hiermee stelt u de minimale uitvoerings tijd in die hierboven wordt vastgelegd voor het vastleggen van autovacuüm-acties
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) -registreert elk controle punt
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) -Hiermee wordt elke geslaagde verbinding geregistreerd
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) : Hiermee stelt u het doel in voor de server logboek uitvoer
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) -logboeken einde van een sessie, inclusief duur
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) : de duur van elke voltooide SQL-instructie wordt geregistreerd
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) : Hiermee stelt u de uitgebreideiteit van geregistreerde berichten in
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) -logboeken met lange wacht tijden vergren delen
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) : Hiermee stelt u de minimale uitvoerings tijd in (in milliseconden) waarboven de instructies worden geregistreerd. -1 de duur van de registratie-instructie wordt uitgeschakeld
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) -zorgt ervoor dat alle instructies die een fout genereren op of hoger dit niveau worden vastgelegd
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) : Hiermee stelt u de bericht niveaus in die zijn geregistreerd
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) -registreert elke replicatie opdracht
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) : Hiermee stelt u het type instructies in dat is geregistreerd
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) : voor elke query schrijft u de cumulatieve prestatie statistieken naar het server logboek
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) : Hiermee wordt het gebruik van tijdelijke bestanden die groter zijn dan dit aantal kilo bytes vastgelegd.
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) : Hiermee stelt u de maximale hoeveelheid geheugen in die moet worden gebruikt voor onderhouds bewerkingen
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) : Hiermee stelt u het maximum aantal parallelle werk rollen in dat kan worden geactiveerd in één keer
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) : het maximum aantal parallelle processen per uitvoerbaar knoop punt instellen
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) : het maximum aantal door predikaten vergrendelde Tuples per pagina instellen
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) : het maximum aantal door predikaten vergrendelde pagina's en Tuples per relatie instellen
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) : Hiermee stelt u de maximale vertraging in voordat query's worden geannuleerd wanneer een hot standby-server de GEARCHIVEERDe wal-gegevens verwerkt
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) : Hiermee stelt u de maximale vertraging in voordat query's worden geannuleerd wanneer een hot standby-server gestreamde wal-gegevens verwerkt
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) -maximum aantal werk rollen voor tabel synchronisatie per abonnement
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) : Hiermee stelt u de wal-grootte in die een controle punt activeert
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) : Hiermee stelt u de minimale hoeveelheid index gegevens voor een parallelle scan in
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) : Hiermee stelt u de minimale grootte in voor het verkleinen van de wal naar
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) : er wordt een waarschuwing verzonden voor constructs die betekenis hebben gewijzigd sinds postgresql 9,4
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) : de schatting van de Planner van de kosten voor het opstarten van werk processen voor parallelle query's instellen
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) : stelt de schatting van de Planner van de kosten voor het door geven van elke tupel (rij) van de werk nemer naar de hoofd-backend in
* [pg_stat_statements. Save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) : slaat pg_stat_statements statistieken op bij het afsluiten van de server
* [pg_stat_statements. track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) : selecteert welke instructies worden bijgehouden door pg_stat_statements
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) : Hiermee wordt bepaald of hulpprogramma opdrachten worden bijgehouden door pg_stat_statements
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) -bij het genereren van SQL-fragmenten, worden alle id's gequoted
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) : stelt de schatting van de kosten van een niet-opeenvolgende opgehaalde schijf pagina van de planner in
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) -beveiliging van rijen inschakelen
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) : Hiermee stelt u de schema zoekopdracht in voor namen die niet geschikt zijn voor het schema
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) : de schatting van de Planner van de kosten van een sequentieel opgehaalde schijf pagina instellen
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) : het gedrag van de sessie instellen voor triggers en regels opnieuw schrijven
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) -veroorzaakt '... ' Teken reeksen voor het behandelen van backslashes letterlijk
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) : Hiermee stelt u de Maxi maal toegestane duur (in milliseconden) van een wille keurige instructie in. 0 schakelt u dit uit
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) -Hiermee worden gesynchroniseerde sequentiële scans ingeschakeld
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) : het synchronisatie niveau van de huidige trans actie instellen
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) -maximum aantal keepalive-retransmits voor TCP
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) tijd tussen het verzenden van TCP-keepalives
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) -tijd tussen TCP/keepalive-verzen ding
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) : Hiermee stelt u het maximum aantal tijdelijke buffers in dat door elke database sessie wordt gebruikt
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) : Hiermee stelt u de tabel ruimte (en) die u wilt gebruiken voor tijdelijke tabellen en sorteert u bestanden
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) -verzamelt informatie over het uitvoeren van opdrachten
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) -verzamelt statistische gegevens over de database activiteit
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) -verzamelt statistische functies op functie niveau voor de database activiteit
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) -statistieken van timing voor data base-I/O-activiteit verzamelen
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) : "expr = null" behandelen als "expr is null"
* vertraging van [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) onderdruk in milliseconden
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) vacuüm beschik bare hoeveelheid vóór Napping
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) -vacuüm kosten voor een pagina dirtied door vacuüm
* [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) -vacuüm kosten voor een pagina gevonden in de buffer cache
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) -vacuüm kosten voor een pagina die niet in de buffer cache is gevonden
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) -aantal trans acties waarmee vacuüm en warme opschoning moeten worden uitgesteld, indien van toepassing
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) -minimale leeftijd waarop vacuüm een tabelrij moet blok keren
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) leeftijd waarop vacuüm de hele tabel moet scannen om Tuples te blok keren
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) -minimale leeftijd waarop vacuüm een MultiXactId in een tabelrij moet blok keren
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) -multixact leeftijd waarop vacuüm de hele tabel moet scannen om Tuples te blok keren
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) : Hiermee stelt u het maximum interval tussen status rapporten van wal-ontvangers in op de primaire
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) -tijd tussen wal-leegmaak acties die zijn uitgevoerd in de wal Writer
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) -hoeveelheid wal geschreven door wal Writer die een flush-bewerking activeert
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) : Hiermee stelt u de hoeveelheid geheugen in die moet worden gebruikt door interne Sorteer bewerkingen en hash-tabellen voordat u naar tijdelijke schijf bestanden schrijft
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) : Hiermee stelt u in hoe binaire waarden moeten worden gecodeerd in XML
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) : Hiermee stelt u in of XML-gegevens in impliciet parseren en serialisatie bewerkingen moeten worden beschouwd als documenten of inhouds fragmenten

## <a name="next-steps"></a>Volgende stappen

* Een andere vorm van configuratie, behalve server parameters, zijn de resource [configuratie opties](concepts-hyperscale-configuration-options.md) in een grootschalige (Citus)-Server groep.
* De onderliggende PostgreSQL-data base heeft ook [configuratie parameters](http://www.postgresql.org/docs/current/static/runtime-config.html).
