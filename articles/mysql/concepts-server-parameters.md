---
title: Server parameters-Azure Database for MySQL
description: Dit onderwerp bevat richt lijnen voor het configureren van server parameters in Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/26/2021
ms.openlocfilehash: 756337ce20c827d0c6549181c20fd843fa60c020
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720950"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Server parameters in Azure Database for MySQL

In dit artikel vindt u overwegingen en richt lijnen voor het configureren van server parameters in Azure Database for MySQL.

## <a name="what-are-server-parameters"></a>Wat zijn server parameters? 

De MySQL-engine biedt veel verschillende server variabelen/-para meters die kunnen worden gebruikt voor het configureren en het afstemmen van engine gedrag. Sommige para meters kunnen dynamisch worden ingesteld tijdens runtime, terwijl andere ' static ' zijn, zodat de server opnieuw moet worden opgestart om toe te passen.

Azure Database for MySQL maakt het mogelijk om de waarden van verschillende MySQL-serverparameters te wijzigen, met behulp van [Azure Portal](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-using-cli.md) en [PowerShell](./howto-configure-server-parameters-using-powershell.md), om te voldoen aan de behoeften van uw workload.

## <a name="configurable-server-parameters"></a>Configureer bare server parameters

De lijst met ondersteunde server parameters groeit voortdurend. Gebruik het tabblad Server parameters in het Azure Portal om de volledige lijst weer te geven en de waarden voor de server parameters te configureren.

Raadpleeg de volgende secties voor meer informatie over de limieten van de verschillende vaak bijgewerkte server parameters. De limieten worden bepaald door de prijs categorie en vCores van de-server.

### <a name="thread-pools"></a>Thread groepen

MySQL wijst traditioneel een thread toe voor elke client verbinding. Naarmate het aantal gelijktijdige gebruikers toeneemt, is er een overeenkomende daling van de prestaties. Veel actieve threads kunnen van invloed zijn op de prestaties aanzienlijk als gevolg van een grotere context omschakeling, thread-conflicten en een onjuiste plaats voor CPU-caches.

Thread groepen die zich op de server functie bevindt en die verschillend zijn van groepsgewijze verbindingen, maximaliseren de prestaties door een dynamische groep werk thread te introduceren die kan worden gebruikt om het aantal actieve threads dat op de server wordt uitgevoerd, te beperken en de thread verloop tot een minimum te beperken. Dit zorgt ervoor dat de server niet meer bronnen heeft of niet meer wordt gecrasht vanwege onvoldoende geheugen. Thread groepen zijn het meest efficiënt voor korte query's en CPU-intensieve workloads, bijvoorbeeld OLTP-workloads.

Raadpleeg voor meer informatie over thread groepen de inleiding tot [thread groepen in azure database for MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)

> [!NOTE]
> De functie thread pool wordt niet ondersteund voor MySQL 5,6-versie. 

### <a name="configuring-the-thread-pool"></a>De thread pool configureren
Als u thread pool wilt inschakelen, werkt `thread_handling` u de server parameter bij naar "pool-of-threads". Deze para meter is standaard ingesteld op `one-thread-per-connection` , wat betekent dat MySQL een nieuwe thread maakt voor elke nieuwe verbinding. Houd er rekening mee dat dit een statische para meter is en dat het opnieuw opstarten van de server moet worden toegepast.

U kunt ook het maximum-en minimum aantal threads in de groep configureren door de volgende server parameters in te stellen: 
- `thread_pool_max_threads`: Deze waarde zorgt ervoor dat er niet meer dan het aantal threads in de pool is.
- `thread_pool_min_threads`: Met deze waarde wordt het aantal threads ingesteld dat moet worden gereserveerd, zelfs nadat de verbindingen zijn gesloten.

Als u de prestatie problemen met korte query's in de thread groep wilt verbeteren, kunt u met Azure Database for MySQL de batch uitvoering inschakelen, waarbij de threads direct na het uitvoeren van een query terug naar de thread groep moeten worden teruggestuurd. de thread blijft actief gedurende korte tijd om te wachten op de volgende query via deze verbinding. De thread voert de query vervolgens snel en eenmaal uit, wacht op het volgende, totdat het totale tijd verbruik van dit proces een drempel waarde overschrijdt. Het batch-uitvoerings gedrag wordt bepaald aan de hand van de volgende server parameters:  

-  `thread_pool_batch_wait_timeout`: Met deze waarde wordt aangegeven hoe lang een thread wacht voordat een andere query kan worden verwerkt.
- `thread_pool_batch_max_time`: Deze waarde bepaalt de maximale duur van een thread die de uitvoering van de query wordt herhaald en er wordt gewacht op de volgende query.

> [!IMPORTANT]
> Test de thread groep voordat u deze inschakelt in de productie omgeving. 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

In Azure Database for MySQL zijn binaire logboeken altijd ingeschakeld (bijvoorbeeld `log_bin` ingesteld op aan). Als u triggers wilt gebruiken, wordt er een fout bericht weer gegeven die vergelijkbaar *is met de superrechts-en binaire logboek registratie is ingeschakeld (mogelijk wilt u de minder veilige `log_bin_trust_function_creators` variabele gebruiken)*. 

De indeling van de binaire logboek registratie is altijd **rij** en alle verbindingen met de server gebruiken **altijd** binaire logboek registratie van rijen. Met binaire logboek registratie op basis van rijen bestaan geen beveiligings problemen en kan binaire logboek registratie niet breken, dus u kunt veilig instellen [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) op **True**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) voor meer informatie over deze para meter.

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Servers die ondersteuning bieden voor Maxi maal 4 TB opslag

|**Prijscategorie**|**vCore (s)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|**Maximum waarde (bytes)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Algemeen gebruik|2|3758096384|134217728|3758096384|
|Algemeen gebruik|4|8053063680|134217728|8053063680|
|Algemeen gebruik|8|16106127360|134217728|16106127360|
|Algemeen gebruik|16|32749125632|134217728|32749125632|
|Algemeen gebruik|32|66035122176|134217728|66035122176|
|Algemeen gebruik|64|132070244352|134217728|132070244352|
|Geoptimaliseerd geheugen|2|7516192768|134217728|7516192768|
|Geoptimaliseerd geheugen|4|16106127360|134217728|16106127360|
|Geoptimaliseerd geheugen|8|32212254720|134217728|32212254720|
|Geoptimaliseerd geheugen|16|65498251264|134217728|65498251264|
|Geoptimaliseerd geheugen|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>Servers ondersteunen Maxi maal 16 TB opslag

|**Prijscategorie**|**vCore (s)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|**Maximum waarde (bytes)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Algemeen gebruik|2|7516192768|134217728|7516192768|
|Algemeen gebruik|4|16106127360|134217728|16106127360|
|Algemeen gebruik|8|32212254720|134217728|32212254720|
|Algemeen gebruik|16|65498251264|134217728|65498251264|
|Algemeen gebruik|32|132070244352|134217728|132070244352|
|Algemeen gebruik|64|264140488704|134217728|264140488704|
|Geoptimaliseerd geheugen|2|15032385536|134217728|15032385536|
|Geoptimaliseerd geheugen|4|32212254720|134217728|32212254720|
|Geoptimaliseerd geheugen|8|64424509440|134217728|64424509440|
|Geoptimaliseerd geheugen|16|130996502528|134217728|130996502528|
|Geoptimaliseerd geheugen|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` kan alleen worden bijgewerkt in de prijs Categorieën Algemeen en geoptimaliseerd voor geheugen.

MySQL slaat de tabel InnoDB op in verschillende tablespaces op basis van de configuratie die u hebt ingevoerd tijdens het maken van de tabel. De [systeem-tabel ruimte](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) is het opslag gebied voor de InnoDB-data dictionary. Een [bestand-per-tabel tabel ruimte](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) bevat gegevens en indexen voor één InnoDB-tabel en wordt opgeslagen in het bestands systeem in een eigen gegevens bestand. Dit gedrag wordt bepaald door de `innodb_file_per_table` para meter server. Instelling `innodb_file_per_table` om `OFF` ervoor te zorgen dat InnoDB tabellen maakt in de ruimte van het systeem. Anders maakt InnoDB tabellen in file-per-tabel tablespaces.

Azure Database for MySQL ondersteunt Maxi maal **4 TB** in één gegevens bestand. Als de grootte van de data base groter is dan 4 TB, maakt u de tabel in [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabel ruimte. Als u één tabel grootte hebt die groter is dan 4 TB, moet u de partitie tabel gebruiken.

### <a name="join_buffer_size"></a>join_buffer_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|**Maximum waarde (bytes)**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|262144|128|268435455|
|Algemeen gebruik|4|262144|128|536870912|
|Algemeen gebruik|8|262144|128|1073741824|
|Algemeen gebruik|16|262144|128|2147483648|
|Algemeen gebruik|32|262144|128|4294967295|
|Algemeen gebruik|64|262144|128|4294967295|
|Geoptimaliseerd geheugen|2|262144|128|536870912|
|Geoptimaliseerd geheugen|4|262144|128|1073741824|
|Geoptimaliseerd geheugen|8|262144|128|2147483648|
|Geoptimaliseerd geheugen|16|262144|128|4294967295|
|Geoptimaliseerd geheugen|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Prijscategorie**|**vCore (s)**|**Standaardwaarde**|**Minimum waarde**|**Maximum waarde**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Algemeen gebruik|2|300|10|600|
|Algemeen gebruik|4|625|10|1250|
|Algemeen gebruik|8|1250|10|2500|
|Algemeen gebruik|16|2500|10|5000|
|Algemeen gebruik|32|5000|10|10.000|
|Algemeen gebruik|64|10.000|10|20.000|
|Geoptimaliseerd geheugen|2|625|10|1250|
|Geoptimaliseerd geheugen|4|1250|10|2500|
|Geoptimaliseerd geheugen|8|2500|10|5000|
|Geoptimaliseerd geheugen|16|5000|10|10.000|
|Geoptimaliseerd geheugen|32|10.000|10|20.000|

Wanneer verbindingen de limiet overschrijden, wordt mogelijk de volgende fout weer gegeven:
> FOUT 1040 (08004): te veel verbindingen

> [!IMPORTANT]
> Voor de beste ervaring raden we u aan een Pooler voor verbindingen te gebruiken zoals ProxySQL om verbindingen efficiënt te beheren.

Het maken van nieuwe client verbindingen met MySQL vergt tijd en deze verbindingen nemen database bronnen in beslag, zelfs wanneer ze niet actief zijn. De meeste toepassingen aanvragen een groot aantal korte, langdurige verbindingen, waardoor deze situatie wordt beperkt. Het resultaat is minder beschik bare resources voor uw werkelijke workload, waardoor de prestaties afnemen. Een verbindings groep waarmee niet-actieve verbindingen worden verminderd en bestaande verbindingen opnieuw worden gebruikt, kunt u dit voor komen. Ga naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)voor meer informatie over het instellen van ProxySQL.

>[!Note]
>ProxySQL is een open source-community-hulp programma. Het wordt door micro soft geadviseerd op basis van de beste inspanningen. Als u ondersteuning voor productie wilt krijgen met behulp van gezaghebbende richt lijnen, kunt u [ProxySQL-product ondersteuning](https://proxysql.com/services/support/)evalueren en bereiken.

### <a name="max_heap_table_size"></a>max_heap_table_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|**Maximum waarde (bytes)**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|16777216|16384|268435455|
|Algemeen gebruik|4|16777216|16384|536870912|
|Algemeen gebruik|8|16777216|16384|1073741824|
|Algemeen gebruik|16|16777216|16384|2147483648|
|Algemeen gebruik|32|16777216|16384|4294967295|
|Algemeen gebruik|64|16777216|16384|4294967295|
|Geoptimaliseerd geheugen|2|16777216|16384|536870912|
|Geoptimaliseerd geheugen|4|16777216|16384|1073741824|
|Geoptimaliseerd geheugen|8|16777216|16384|2147483648|
|Geoptimaliseerd geheugen|16|16777216|16384|4294967295|
|Geoptimaliseerd geheugen|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

De query cache is standaard uitgeschakeld. Configureer de para meter om de query-cache in te scha kelen `query_cache_type` . 

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) voor meer informatie over deze para meter.

> [!NOTE]
> De query cache is afgeschaft vanaf MySQL 5.7.20 installeren en is verwijderd in MySQL 8,0

|**Prijscategorie**|**vCore (s)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|* * Maximum waarde * *|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|0|0|16777216|
|Algemeen gebruik|4|0|0|33554432|
|Algemeen gebruik|8|0|0|67108864|
|Algemeen gebruik|16|0|0|134217728|
|Algemeen gebruik|32|0|0|134217728|
|Algemeen gebruik|64|0|0|134217728|
|Geoptimaliseerd geheugen|2|0|0|33554432|
|Geoptimaliseerd geheugen|4|0|0|67108864|
|Geoptimaliseerd geheugen|8|0|0|134217728|
|Geoptimaliseerd geheugen|16|0|0|134217728|
|Geoptimaliseerd geheugen|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

De lower_case_table_name is standaard ingesteld op 1 en u kunt deze para meter bijwerken in MySQL 5,6 en MySQL 5,7

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) voor meer informatie over deze para meter.

> [!NOTE]
> In MySQL 8,0 is de lower_case_table_name standaard ingesteld op 1 en kunt u deze niet wijzigen.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Als er een fout bericht wordt weer gegeven dat vergelijkbaar is met het ' Rijgrootte is te groot (> 8126) ', kunt u de para meter **INNODB_STRICT_MODE** uitschakelen. De **innodb_strict_mode** van de server parameter mag niet globaal worden gewijzigd op server niveau omdat de grootte van de gegevensrij groter is dan 8k, worden de gegevens afgekapt zonder dat er een fout optreedt bij mogelijk gegevens verlies. Het is raadzaam om het schema aan te passen aan de limiet voor de pagina grootte. 

Deze para meter kan worden ingesteld op sessie niveau met `init_connect` . Als u **innodb_strict_mode** wilt instellen op sessie niveau, raadpleegt u de [instellings parameter wordt niet weer gegeven](./howto-server-parameters.md#setting-parameters-not-listed).

> [!NOTE]
> Als u een replica-server lezen hebt ingesteld, wordt het instellen van **innodb_strict_mode** op sessie niveau op een bron server uitgeschakeld. U wordt aangeraden de para meter in te stellen op uit als u replica's hebt gelezen.

### <a name="sort_buffer_size"></a>sort_buffer_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|**Maximum waarde (bytes)**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|524288|32768|4194304|
|Algemeen gebruik|4|524288|32768|8388608|
|Algemeen gebruik|8|524288|32768|16777216|
|Algemeen gebruik|16|524288|32768|33554432|
|Algemeen gebruik|32|524288|32768|33554432|
|Algemeen gebruik|64|524288|32768|33554432|
|Geoptimaliseerd geheugen|2|524288|32768|8388608|
|Geoptimaliseerd geheugen|4|524288|32768|16777216|
|Geoptimaliseerd geheugen|8|524288|32768|33554432|
|Geoptimaliseerd geheugen|16|524288|32768|33554432|
|Geoptimaliseerd geheugen|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|**Maximum waarde (bytes)**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|16777216|1024|67108864|
|Algemeen gebruik|4|16777216|1024|134217728|
|Algemeen gebruik|8|16777216|1024|268435456|
|Algemeen gebruik|16|16777216|1024|536870912|
|Algemeen gebruik|32|16777216|1024|1073741824|
|Algemeen gebruik|64|16777216|1024|1073741824|
|Geoptimaliseerd geheugen|2|16777216|1024|134217728|
|Geoptimaliseerd geheugen|4|16777216|1024|268435456|
|Geoptimaliseerd geheugen|8|16777216|1024|536870912|
|Geoptimaliseerd geheugen|16|16777216|1024|1073741824|
|Geoptimaliseerd geheugen|32|16777216|1024|1073741824|

### <a name="innodb-buffer-pool-warmup"></a>Opwarm InnoDB-buffer groep
Nadat Azure Database for MySQL server opnieuw is opgestart, worden de gegevens pagina's geladen die zich in de schijf bevinden. Dit leidt tot betere latentie en tragere prestaties voor de eerste uitvoering van de query's. Dit is mogelijk niet acceptabel voor latentie gevoelige werk belastingen. Het gebruik van de InnoDB-buffer groep opwarm verkort de opwarm periode door schijf pagina's opnieuw te laden die zich in de buffer groep bevonden vóór het opnieuw opstarten, in plaats van te wachten op DML of SELECT-bewerkingen om toegang te krijgen tot overeenkomende rijen.

U kunt de opwarm-periode verminderen nadat u uw Azure Database for MySQL-server opnieuw hebt opgestart, wat een prestatie voordeel voor stelt door [InnoDB-buffer pool server parameters](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)te configureren. InnoDB slaat een percentage van de meest recent gebruikte pagina's op voor elke buffer groep bij het afsluiten van de server en herstelt deze pagina's bij het opstarten van de server.

Het is ook belang rijk om te weten dat verbeterde prestaties ten koste gaan van een langere opstart tijd voor de-server. Als deze para meter is ingeschakeld, worden de opstart-en start tijd van de server naar verwachting verhoogd, afhankelijk van de IOPS die op de server is ingericht. We raden u aan om de herstarttijd te testen en te controleren om ervoor te zorgen dat de prestaties van de opstart-en start periode acceptabel zijn omdat de server gedurende die tijd niet beschikbaar is. Het wordt niet aangeraden deze para meter te gebruiken wanneer het aantal IOPS dat is ingericht, kleiner is dan 1000 IOPS (of met andere woorden, wanneer de ingerichte opslag ruimte lager is dan 335GB.

Als u de status van de buffer groep wilt opslaan bij Server para meter instellen voor het afsluiten van servers `innodb_buffer_pool_dump_at_shutdown` in `ON` . Stel op dezelfde manier de server parameter in om `innodb_buffer_pool_load_at_startup` `ON` de status van de buffer groep te herstellen bij het opstarten van de server. U kunt de gevolgen voor het opstarten en opnieuw opstarten bepalen door de waarde van de server parameter te verlagen en te verfijnen `innodb_buffer_pool_dump_pct` . deze para meter is standaard ingesteld op `25` .

> [!Note]
> De opwarm-para meters van de InnoDB-buffer groep worden alleen ondersteund in de opslag servers voor algemeen gebruik met Maxi maal 16 TB opslag. Meer informatie over [Azure database for MySQL opslag opties vindt u hier](./concepts-pricing-tiers.md#storage).

### <a name="time_zone"></a>time_zone

Bij de eerste implementatie bevat een Azure voor MySQL-server systeem tabellen voor tijdzone gegevens, maar deze tabellen worden niet ingevuld. De tijdzone tabellen kunnen worden gevuld door de `mysql.az_load_timezone` opgeslagen procedure aan te roepen vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. Raadpleeg de [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) -of [Azure cli](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) -artikelen voor informatie over het aanroepen van de opgeslagen procedure en het instellen van de tijd zones globaal of sessie niveau.

## <a name="non-configurable-server-parameters"></a>Niet-Configureer bare server parameters

De onderstaande server parameters kunnen niet worden geconfigureerd in de service:

|**Parameter**|**Vaste waarde**|
| :------------------------ | :-------- |
|innodb_file_per_table in de Basic-laag|UIT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Andere variabelen die hier niet worden vermeld, worden ingesteld op de standaard waarden van het vak voor MySQL. Raadpleeg de MySQL-documenten voor versie [8,0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5,7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)en [5,6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) voor de standaard waarden. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het configureren van server parameters met behulp van de Azure Portal](./howto-server-parameters.md)
- Meer informatie over [het configureren van de para meters voor de server met behulp van Azure cli](./howto-configure-server-parameters-using-cli.md)
- Meer informatie over het [configureren van de para meters voor de server met Power shell](./howto-configure-server-parameters-using-powershell.md)