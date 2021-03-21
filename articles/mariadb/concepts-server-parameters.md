---
title: Server parameters-Azure Database for MariaDB
description: Dit onderwerp bevat richt lijnen voor het configureren van server parameters in Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 7797ee9d20b33a25c1b51289036651c7ad9f22a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664143"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Server parameters in Azure Database for MariaDB

In dit artikel vindt u overwegingen en richt lijnen voor het configureren van server parameters in Azure Database for MariaDB.

## <a name="what-are-server-parameters"></a>Wat zijn server parameters? 

De MariaDB-engine biedt een groot aantal verschillende server variabelen/-para meters die kunnen worden gebruikt voor het configureren en het afstemmen van engine gedrag. Sommige para meters kunnen dynamisch worden ingesteld tijdens runtime, terwijl andere ' static ' zijn, zodat de server opnieuw moet worden opgestart om toe te passen.

Azure Database for MariaDB geeft de mogelijkheid om de waarde van verschillende MariaDB-server parameters te wijzigen met behulp van de [Azure Portal](./howto-server-parameters.md), [Azure cli](./howto-configure-server-parameters-cli.md)en [Power shell](./howto-configure-server-parameters-using-powershell.md) , die overeenkomen met de behoeften van uw werk belasting.

## <a name="configurable-server-parameters"></a>Configureer bare server parameters

De lijst met ondersteunde server parameters groeit voortdurend. Gebruik het tabblad Server parameters in het Azure Portal om de volledige lijst weer te geven en de waarden voor de server parameters te configureren.

Raadpleeg de volgende secties voor meer informatie over de limieten van de verschillende vaak bijgewerkte server parameters. De limieten worden bepaald door de prijs categorie en vCores van de-server.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

In Azure Database for MariaDB zijn binaire logboeken altijd ingeschakeld (bijvoorbeeld `log_bin` ingesteld op aan). Als u triggers wilt gebruiken, wordt er een fout bericht weer gegeven die vergelijkbaar *is met de superrechts-en binaire logboek registratie is ingeschakeld (mogelijk wilt u de minder veilige `log_bin_trust_function_creators` variabele gebruiken)*.

De indeling van de binaire logboek registratie is altijd **rij** en alle verbindingen met de server gebruiken **altijd** binaire logboek registratie van rijen. Met binaire logboek registratie op basis van rijen bestaan geen beveiligings problemen en kan binaire logboek registratie niet breken, dus u kunt veilig instellen [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) op **True**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size) voor meer informatie over deze para meter.

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

MariaDB slaat de InnoDB-tabel op in verschillende tablespaces op basis van de configuratie die u hebt gemaakt tijdens het maken van de tabel. De [systeem-tabel ruimte](https://mariadb.com/kb/en/innodb-system-tablespaces/) is het opslag gebied voor de InnoDB-data dictionary. Een [bestand-per-tabel tabel ruimte](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) bevat gegevens en indexen voor één InnoDB-tabel en wordt opgeslagen in het bestands systeem in een eigen gegevens bestand. Dit gedrag wordt bepaald door de `innodb_file_per_table` para meter server. Instelling `innodb_file_per_table` om `OFF` ervoor te zorgen dat InnoDB tabellen maakt in de ruimte van het systeem. Anders maakt InnoDB tabellen in file-per-tabel tablespaces.

Azure Database for MariaDB ondersteunt Maxi maal **1 TB** in één gegevens bestand. Als de grootte van de data base groter is dan 1 TB, maakt u de tabel in [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) tabel ruimte. Als u één tabel grootte hebt die groter is dan 1 TB, moet u de partitie tabel gebruiken.

### <a name="join_buffer_size"></a>join_buffer_size

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) voor meer informatie over deze para meter.

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

Het maken van nieuwe client verbindingen met MariaDB kost tijd en wanneer de verbinding tot stand is gebracht, nemen deze verbindingen database bronnen in beslag, zelfs wanneer ze niet actief zijn De meeste toepassingen aanvragen een groot aantal korte, langdurige verbindingen, waardoor deze situatie wordt beperkt. Het resultaat is minder beschik bare resources voor uw werkelijke workload, waardoor de prestaties afnemen. Een verbindings groep waarmee niet-actieve verbindingen worden verminderd en bestaande verbindingen opnieuw worden gebruikt, kunt u dit voor komen. Ga naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)voor meer informatie over het instellen van ProxySQL.

>[!Note]
>ProxySQL is een open source-community-hulp programma. Het wordt door micro soft geadviseerd op basis van de beste inspanningen. Als u ondersteuning voor productie wilt krijgen met behulp van gezaghebbende richt lijnen, kunt u [ProxySQL-product ondersteuning](https://proxysql.com/services/support/)evalueren en bereiken.

### <a name="max_heap_table_size"></a>max_heap_table_size

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) voor meer informatie over deze para meter.

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

De query cache is standaard ingeschakeld in MariaDB met de `have_query_cache` para meter. 

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|**Maximum waarde (bytes)**|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) voor meer informatie over deze para meter.

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

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) voor meer informatie over deze para meter.

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

### <a name="time_zone"></a>time_zone

Bij de eerste implementatie bevat een Azure voor MariaDB-server systeem tabellen voor tijdzone gegevens, maar deze tabellen worden niet ingevuld. De tijdzone tabellen kunnen worden gevuld door de `mysql.az_load_timezone` opgeslagen procedure aan te roepen vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. Raadpleeg de [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) -of [Azure cli](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -artikelen voor informatie over het aanroepen van de opgeslagen procedure en het instellen van de tijd zones globaal of sessie niveau.

## <a name="non-configurable-server-parameters"></a>Niet-Configureer bare server parameters

De onderstaande server parameters kunnen niet worden geconfigureerd in de service:

|**Parameter**|**Vaste waarde**|
| :------------------------ | :-------- |
|innodb_file_per_table in de Basic-laag|UIT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Andere server parameters die hier niet worden vermeld, worden ingesteld op hun MariaDB-out-of-Box-standaard waarden voor [MariaDB](https://mariadb.com/kb/en/server-system-variables/).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het configureren van server parameters met behulp van de Azure Portal](./howto-server-parameters.md)
- Meer informatie over [het configureren van de para meters voor de server met behulp van Azure cli](./howto-configure-server-parameters-cli.md)
- Meer informatie over het [configureren van de para meters voor de server met Power shell](./howto-configure-server-parameters-using-powershell.md)
