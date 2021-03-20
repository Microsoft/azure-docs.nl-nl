---
title: Server parameters-Azure Database for MySQL-flexibele server
description: Dit onderwerp bevat richt lijnen voor het configureren van server parameters in Azure Database for MySQL-flexibele server.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 58978f120578afeca129b0d8928713835def8418
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94496743"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Server parameters in Azure Database for MySQL-flexibele server

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel vindt u overwegingen en richt lijnen voor het configureren van server parameters in Azure Database for MySQL flexibele server.

## <a name="what-are-server-variables"></a>Wat zijn server variabelen? 

De MySQL-engine biedt veel verschillende [Server variabelen/-para meters](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) die kunnen worden gebruikt voor het configureren en het afstemmen van engine gedrag. Sommige para meters kunnen dynamisch worden ingesteld tijdens runtime, terwijl andere ' static ' zijn, zodat de server opnieuw moet worden opgestart om toe te passen.

Azure Database for MySQL flexibele server biedt de mogelijkheid om de waarde van verschillende MySQL-server parameters te wijzigen met behulp van de [Azure Portal](./how-to-configure-server-parameters-portal.md) en [Azure cli](./how-to-configure-server-parameters-cli.md) die overeenkomen met de behoeften van uw werk belasting.

## <a name="configurable-server-parameters"></a>Configureer bare server parameters

U kunt Azure Database for MySQL flexibele server configuratie beheren met server parameters. De server parameters worden geconfigureerd met de standaard-en aanbevolen waarde bij het maken van de server. De Blade Server parameters op Azure Portal bevat zowel de para meters die kunnen worden gewijzigd en die niet kunnen worden gewijzigd. De niet-aanpas bare server parameters worden grijs weer gegeven.

De lijst met ondersteunde server parameters groeit voortdurend. Gebruik het tabblad Server parameters in het Azure Portal om de volledige lijst weer te geven en de waarden voor de server parameters te configureren.

Raadpleeg de volgende secties voor meer informatie over de limieten van de verschillende vaak bijgewerkte server parameters. De limieten worden bepaald door de compute-laag en grootte (vCores) van de-server.

> [!NOTE]
> Als u een server parameter wilt wijzigen die niet kan worden gewijzigd, maar u wilt zien dat deze in uw omgeving kan worden gewijzigd, opent u een [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) -item of stem als de feedback al bestaat.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

In Azure Database for MySQL flexibele server zijn binaire logboeken altijd ingeschakeld (dat wil zeggen, `log_bin` is ingesteld op aan). Als u triggers wilt gebruiken, wordt er een fout bericht weer gegeven die vergelijkbaar *is met de superrechts-en binaire logboek registratie is ingeschakeld (mogelijk wilt u de minder veilige `log_bin_trust_function_creators` variabele gebruiken)*. 

De indeling van de binaire logboek registratie is altijd **rij** en alle verbindingen met de server gebruiken **altijd** binaire logboek registratie van rijen. Met binaire logboek registratie op basis van rijen bestaan geen beveiligings problemen en kan binaire logboek registratie niet breken, dus u kunt veilig instellen [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) op **True**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Geheugen grootte (GiB)**|**Standaard waarde (bytes)**|**Minimum waarde (bytes)**|**Maximum waarde (bytes)**|
|---|---|---|---|---|---|
|Burstable (B1s)|1|1|134217728|33554432|134217728|
|Burstable (B1ms)|1|2|536870912|134217728|536870912|
|Bebreekbaar|2|4|2147483648|134217728|2147483648|
|Algemeen gebruik|2|8|6442450944|134217728|6442450944|
|Algemeen gebruik|4|16|12884901888|134217728|12884901888|
|Algemeen gebruik|8|32|25769803776|134217728|25769803776|
|Algemeen gebruik|16|64|51539607552|134217728|51539607552|
|Algemeen gebruik|32|128|103079215104|134217728|103079215104|
|Algemeen gebruik|48|192|154618822656|134217728|154618822656|
|Algemeen gebruik|64|256|206158430208|134217728|206158430208|
|Geoptimaliseerd geheugen|2|16|12884901888|134217728|12884901888|
|Geoptimaliseerd geheugen|4|32|25769803776|134217728|25769803776|
|Geoptimaliseerd geheugen|8|64|51539607552|134217728|51539607552|
|Geoptimaliseerd geheugen|16|128|103079215104|134217728|103079215104|
|Geoptimaliseerd geheugen|32|256|206158430208|134217728|206158430208|
|Geoptimaliseerd geheugen|48|384|309237645312|134217728|309237645312|
|Geoptimaliseerd geheugen|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL slaat de tabel InnoDB op in verschillende tablespaces op basis van de configuratie die u hebt ingevoerd tijdens het maken van de tabel. De [systeem-tabel ruimte](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) is het opslag gebied voor de InnoDB-data dictionary. Een [bestand-per-tabel tabel ruimte](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) bevat gegevens en indexen voor één InnoDB-tabel en wordt opgeslagen in het bestands systeem in een eigen gegevens bestand. Dit gedrag wordt bepaald door de `innodb_file_per_table` para meter server. Instelling `innodb_file_per_table` om `OFF` ervoor te zorgen dat InnoDB tabellen maakt in de ruimte van het systeem. Anders maakt InnoDB tabellen in file-per-tabel tablespaces.

Azure Database for MySQL flexibele server ondersteunt Maxi maal **4 TB** in één gegevens bestand. Als de grootte van de data base groter is dan 4 TB, maakt u de tabel in [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabel ruimte. Als u één tabel grootte hebt die groter is dan 4 TB, moet u de partitie tabel gebruiken.

### <a name="max_connections"></a>max_connections

De waarde van max_connection wordt bepaald door de geheugen grootte van de server. 

|**Prijscategorie**|**vCore (s)**|**Geheugen grootte (GiB)**|**Standaardwaarde**|**Minimum waarde**|**Maximum waarde**|
|---|---|---|---|---|---|
|Burstable (B1s)|1|1|85|10|171|
|Burstable (B1ms)|1|2|171|10|341|
|Bebreekbaar|2|4|341|10|683|
|Algemeen gebruik|2|8|683|10|1365|
|Algemeen gebruik|4|16|1365|10|2731|
|Algemeen gebruik|8|32|2731|10|5461|
|Algemeen gebruik|16|64|5461|10|10923|
|Algemeen gebruik|32|128|10923|10|21845|
|Algemeen gebruik|48|192|16384|10|32768|
|Algemeen gebruik|64|256|21845|10|43691|
|Geoptimaliseerd geheugen|2|16|1365|10|2731|
|Geoptimaliseerd geheugen|4|32|2731|10|5461|
|Geoptimaliseerd geheugen|8|64|5461|10|10923|
|Geoptimaliseerd geheugen|16|128|10923|10|21845|
|Geoptimaliseerd geheugen|32|256|21845|10|43691|
|Geoptimaliseerd geheugen|48|384|32768|10|65536|
|Geoptimaliseerd geheugen|64|504|43008|10|86016|

Wanneer verbindingen de limiet overschrijden, wordt mogelijk de volgende fout weer gegeven:
> FOUT 1040 (08004): te veel verbindingen

> [!IMPORTANT]
> Voor de beste ervaring raden we u aan een Pooler voor verbindingen te gebruiken zoals ProxySQL om verbindingen efficiënt te beheren.

Het maken van nieuwe client verbindingen met MySQL vergt tijd en deze verbindingen nemen database bronnen in beslag, zelfs wanneer ze niet actief zijn. De meeste toepassingen aanvragen een groot aantal korte, langdurige verbindingen, waardoor deze situatie wordt beperkt. Het resultaat is minder beschik bare resources voor uw werkelijke workload, waardoor de prestaties afnemen. Een verbindings groep waarmee niet-actieve verbindingen worden verminderd en bestaande verbindingen opnieuw worden gebruikt, kunt u dit voor komen. Ga naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)voor meer informatie over het instellen van ProxySQL.

>[!Note]
>ProxySQL is een open source-community-hulp programma. Het wordt door micro soft geadviseerd op basis van de beste inspanningen. Als u ondersteuning voor productie wilt krijgen met behulp van gezaghebbende richt lijnen, kunt u [ProxySQL-product ondersteuning](https://proxysql.com/services/support/)evalueren en bereiken.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Als er een fout bericht wordt weer gegeven dat vergelijkbaar is met het ' Rijgrootte te groot (> 8126) ', kunt u de para meter **INNODB_STRICT_MODE** uitschakelen. De **innodb_strict_mode** van de server parameter mag niet globaal worden gewijzigd op server niveau omdat de grootte van de gegevensrij groter is dan 8k, worden de gegevens afgekapt zonder een fout, wat kan leiden tot mogelijk gegevens verlies. Het is raadzaam om het schema aan te passen aan de limiet voor de pagina grootte. 

Deze para meter kan worden ingesteld op sessie niveau met `init_connect` . Als u **innodb_strict_mode** wilt instellen op sessie niveau, raadpleegt u de [instellings parameter wordt niet weer gegeven](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters).

> [!NOTE]
> Als u een replica-server lezen hebt ingesteld, wordt het instellen van **innodb_strict_mode** op sessie niveau op een bron server uitgeschakeld. U wordt aangeraden de para meter in te stellen op uit als u replica's hebt gelezen.

### <a name="time_zone"></a>time_zone

Bij de eerste implementatie bevat een flexibele server van Azure voor MySQL systeem tabellen voor tijdzone gegevens, maar deze tabellen worden niet ingevuld. De tijdzone tabellen kunnen worden gevuld door de `mysql.az_load_timezone` opgeslagen procedure aan te roepen vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. Raadpleeg de [Azure Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) -of [Azure cli](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -artikelen voor informatie over het aanroepen van de opgeslagen procedure en het instellen van de tijd zones globaal of sessie niveau.

## <a name="non-modifiable-server-parameters"></a>Niet-aanpas bare server parameters

De Blade Server parameters op het Azure Portal bevat zowel de para meters die kunnen worden gewijzigd en die niet kunnen worden gewijzigd. De niet-aanpas bare server parameters worden grijs weer gegeven. Als u een niet-aanpas bare server parameter op sessie niveau wilt configureren, raadpleegt u het artikel over [Azure Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) of [Azure cli](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) voor het instellen van de para meter op het verbindings niveau met `init_connect` .

## <a name="next-steps"></a>Volgende stappen

- [Server parameters configureren in azure Portal](./how-to-configure-server-parameters-portal.md)
- [Server parameters configureren in azure cli](./how-to-configure-server-parameters-cli.md)
