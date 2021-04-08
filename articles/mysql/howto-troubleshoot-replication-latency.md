---
title: Problemen met replicatie latentie Azure Database for MySQL
description: Meer informatie over het oplossen van replicatie latentie met behulp van Azure Database for MySQL replica's te lezen.
keywords: MySQL, problemen oplossen, replicatie latentie in seconden
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: 92513a8c24b5106e3a59c8cfa4d743e900b957bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98249768"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Troubleshoot replication latency in Azure Database for MySQL (Problemen vanwege replicavertraging oplossen in Azure Database for MySQL)

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

Met de functie [replica lezen](concepts-read-replicas.md) kunt u gegevens van een Azure database for mysql server repliceren naar een replica server met het kenmerk alleen-lezen. U kunt werk belastingen uitschalen door lees-en rapport query's van de toepassing naar replica servers te routeren. Deze installatie vermindert de druk op de bron server. Het verbetert ook de algehele prestaties en latentie van de toepassing terwijl deze wordt geschaald.

Replica's worden asynchroon bijgewerkt door gebruik te maken van de systeem eigen binaire logboek bestand (binlog) van de MySQL-engine. Zie voor meer informatie [het overzicht van de configuratie van het MySQL binlog-bestand op basis van de replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

De replicatie vertraging op de secundaire Lees replica's is afhankelijk van verschillende factoren. Deze factoren omvatten, maar zijn niet beperkt tot:

- Netwerk latentie.
- Trans actie-volume op de bron server.
- Compute-laag van de bron server en secundaire replica server voor lezen.
- Query's die worden uitgevoerd op de bron server en de secundaire server.

In dit artikel leert u hoe u de replicatie latentie in Azure Database for MySQL kunt oplossen. U begrijpt ook enkele veelvoorkomende oorzaken van verhoogde replicatie latentie op replica servers.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
>

## <a name="replication-concepts"></a>Concepten voor replicatie

Wanneer een binair logboek is ingeschakeld, schrijft de bron server doorgevoerde trans acties naar het binaire logboek. Het binaire logboek wordt gebruikt voor replicatie. Het is standaard ingeschakeld voor alle nieuw ingerichte servers die ondersteuning bieden voor Maxi maal 16 TB aan opslag ruimte. Op replica servers worden twee threads uitgevoerd op elke replica-server. De ene thread is de *i/o-thread* en de andere is de *SQL-thread*:

- De IO-thread maakt verbinding met de bron server en vraagt bijgewerkte binaire logboeken aan. Deze thread ontvangt de binaire logboek updates. Deze updates worden opgeslagen op een replica server in een lokaal logboek dat het *Relay-logboek* wordt genoemd.
- De SQL-thread leest het relay-logboek en past vervolgens de gegevens wijzigingen op replica servers toe.

## <a name="monitoring-replication-latency"></a>Replicatie latentie controleren

Azure Database for MySQL geeft de metriek voor replicatie vertraging in seconden in [Azure monitor](concepts-monitoring.md). Deze metriek is alleen beschikbaar op replica servers lezen. Het wordt berekend door de seconds_behind_master metriek die beschikbaar is in MySQL. 

Als u wilt weten wat de oorzaak is van een verhoogde replicatie latentie, maakt u verbinding met de replica server met behulp van [MySQL Workbench](connect-workbench.md) of [Azure Cloud shell](https://shell.azure.com). Voer vervolgens de volgende opdracht uit.

>[!NOTE]
> Vervang de voorbeeld waarden in uw code door de naam van de replica server en de gebruikers naam van de beheerder. De gebruikers naam van de beheerder vereist `@\<servername>` voor Azure database for MySQL.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Zo ziet het proces eruit in de Cloud Shell-terminal:

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

Voer in hetzelfde Cloud Shell-Terminal de volgende opdracht uit:

```
mysql> SHOW SLAVE STATUS;
```

Hier volgt een typische uitvoer:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Replicatie latentie controleren":::

De uitvoer bevat veel informatie. Normaal gesp roken moet u zich richten op de rijen die in de volgende tabel worden beschreven.

|Metrisch|Beschrijving|
|---|---|
|Slave_IO_State| Hiermee wordt de huidige status van de IO-thread aangeduid. Normaal gesp roken is de status ' wachten op het verzenden van de hoofd gebeurtenis ' als de bron server (Master) synchroniseert. Een status zoals ' verbinding maken met Master ' geeft aan dat de replica de verbinding met de bron server heeft verloren. Zorg ervoor dat de bron server wordt uitgevoerd of Controleer of de verbinding door een firewall wordt geblokkeerd.|
|Master_Log_File| Vertegenwoordigt het binaire logboek bestand waarnaar de bron server wordt geschreven.|
|Read_Master_Log_Pos| Hiermee wordt aangegeven waar de bron server naar het binaire logboek bestand schrijft.|
|Relay_Master_Log_File| Vertegenwoordigt het binaire logboek bestand dat door de replica server wordt gelezen van de bron server.|
|Slave_IO_Running| Hiermee wordt aangegeven of de IO-thread wordt uitgevoerd. De waarde moet zijn `Yes` . Als de waarde is `NO` , is de replicatie waarschijnlijk verbroken.|
|Slave_SQL_Running| Hiermee wordt aangegeven of de SQL-thread wordt uitgevoerd. De waarde moet zijn `Yes` . Als de waarde is `NO` , is de replicatie waarschijnlijk verbroken.|
|Exec_Master_Log_Pos| Hiermee wordt de positie aangegeven van de Relay_Master_Log_File waarop de replica wordt toegepast. Als er sprake is van een latentie, moet deze positie reeks kleiner zijn dan Read_Master_Log_Pos.|
|Relay_Log_Space|Hiermee wordt de bovengrens van de grootte van het relay-logboek aangegeven. U kunt de grootte controleren door een query uit te `SHOW GLOBAL VARIABLES` vinden zoals `relay_log_space_limit` .|
|Seconds_Behind_Master| Hiermee wordt de replicatie latentie in seconden weer gegeven.|
|Last_IO_Errno|Hier wordt de fout code voor de IO-thread weer gegeven, indien aanwezig. Zie voor meer informatie over deze codes de [referentie mysql server-fout berichten](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_IO_Error| Hiermee wordt het fout bericht van de IO-thread weer gegeven, indien aanwezig.|
|Last_SQL_Errno|Hier wordt de fout code van de SQL-thread weer gegeven, indien van toepassing. Zie voor meer informatie over deze codes de [referentie mysql server-fout berichten](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Hiermee wordt het fout bericht van de SQL-thread weer gegeven, indien aanwezig.|
|Slave_SQL_Running_State| Hiermee wordt de huidige status van de SQL-thread aangegeven. In deze status `System lock` is normaal. Het is ook normaal om de status weer te geven `Waiting for dependent transaction to commit` . Deze status geeft aan dat de replica wacht op het bijwerken van doorgevoerde trans acties door de bron server.|

Als Slave_IO_Running `Yes` en Slave_SQL_Running is `Yes` , wordt de replicatie prima uitgevoerd. 

Controleer vervolgens Last_IO_Errno, Last_IO_Error, Last_SQL_Errno en Last_SQL_Error.  Deze velden bevatten het fout nummer en het fout bericht van de meest recente fout die heeft geleid tot het stoppen van de SQL-thread. Als er een fout nummer `0` en een leeg bericht wordt weer gegeven, is er geen fout. Onderzoek een andere fout waarde dan nul door de fout code in de [Referentie van het mysql-server fout bericht](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)te controleren.

## <a name="common-scenarios-for-high-replication-latency"></a>Algemene scenario's voor hoge replicatie latentie

De volgende secties bevatten een overzicht van de scenario's waarin een hoge replicatie latentie gebruikelijk is.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Netwerk latentie of hoog CPU-gebruik op de bron server

Als u de volgende waarden ziet, wordt de replicatie latentie waarschijnlijk veroorzaakt door een hoge netwerk latentie of een hoog CPU-verbruik op de bron server.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

In dit geval wordt de i/o-thread uitgevoerd en wacht op de bron server. De bron server is al geschreven naar het binaire logboek bestand 20. De replica heeft alleen een bericht ontvangen van Maxi maal het bestands nummer 10. De belangrijkste factoren voor een hoge replicatie latentie in dit scenario zijn de netwerk snelheid of het hoge CPU-gebruik op de bron server.  

In azure kan de netwerk latentie binnen een regio doorgaans in milliseconden worden gemeten. In verschillende regio's bereiken latentie tussen milliseconden en seconden.

In de meeste gevallen wordt de verbindings vertraging tussen de i/o-threads en de bron server veroorzaakt door een hoog CPU-gebruik op de bron server. De i/o-threads worden langzaam verwerkt. U kunt dit probleem detecteren met behulp van Azure Monitor om het CPU-gebruik en het aantal gelijktijdige verbindingen op de bron server te controleren.

Als u geen hoog CPU-gebruik op de bron server ziet, is het probleem mogelijk netwerk latentie. Als de netwerk latentie abnormaal hoog is, controleert u de [status pagina van Azure](https://status.azure.com/status) op bekende problemen of storingen. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Zware bursts van trans acties op de bron server

Als u de volgende waarden ziet, wordt de replicatie latentie waarschijnlijk veroorzaakt door een zware burst van trans acties op de bron server. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

De uitvoer laat zien dat de replica het binaire logboek achter de bron server kan ophalen. Maar de replica-IO-thread geeft aan dat de relay-logboek ruimte al vol is.

De netwerk snelheid veroorzaakt niet de vertraging. De replica wordt geprobeerd op te halen. De bijgewerkte binaire logboek grootte overschrijdt echter de bovengrens van de logboek ruimte van de relay.

U kunt dit probleem oplossen door het [langzame query logboek](concepts-server-logs.md) op de bron server in te scha kelen. Gebruik langzame query Logboeken om langlopende trans acties op de bron server te identificeren. Stem vervolgens de geïdentificeerde query's af om de latentie op de server te verminderen. 

De replicatie latentie van deze sortering wordt meestal veroorzaakt door het laden van gegevens op de bron server. Wanneer bron servers wekelijks of maandelijks gegevens laadt, is replicatie latentie helaas niet te voor komen. De replica servers die uiteindelijk worden uitgevoerd nadat het laden van de gegevens op de bron server is voltooid.

### <a name="slowness-on-the-replica-server"></a>Vertraging op de replica-server

Als u de volgende waarden bekijkt, is het probleem mogelijk op de replica server.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

In dit scenario ziet u dat zowel de IO-thread als de SQL-thread goed worden uitgevoerd. De replica leest het binaire logboek bestand dat door de bron server wordt geschreven. Sommige latentie op de replica-server weerspiegelt echter dezelfde trans actie van de bron server.

In de volgende secties worden veelvoorkomende oorzaken van dit type latentie beschreven.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>Geen primaire sleutel of unieke sleutel voor een tabel

Azure Database for MySQL maakt gebruik van op rijen gebaseerde replicatie. De bron server schrijft gebeurtenissen naar het binaire logboek, waarbij wijzigingen in afzonderlijke tabel rijen worden vastgelegd. De SQL-thread repliceert die wijzigingen vervolgens naar de corresponderende tabel rijen op de replica-server. Wanneer een tabel geen primaire sleutel of unieke sleutel heeft, scant de SQL-thread alle rijen in de doel tabel om de wijzigingen toe te passen. Deze scan kan replicatievertraging veroorzaken.

In MySQL is de primaire sleutel een gekoppelde index die snelle query prestaties garandeert, omdat deze geen NULL-waarden kan bevatten. Als u de InnoDB-opslag engine gebruikt, zijn de tabel gegevens fysiek ingedeeld zodat ze zeer snel kunnen worden gevonden en gesorteerd op basis van de primaire sleutel.

U wordt aangeraden een primaire sleutel voor tabellen toe te voegen aan de bron server voordat u de replica-server maakt. Primaire sleutels toevoegen op de bron server en vervolgens lees replica's opnieuw maken om de replicatie latentie te verbeteren.

Gebruik de volgende query om erachter te komen welke tabellen een primaire sleutel op de bron server ontbreken:

```sql
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>Langlopende query's op de replica server

De werk belasting op de replica server kan de SQL-thread vertraging achter de i/o-thread maken. Langlopende query's op de replica server zijn een van de veelvoorkomende oorzaken van een hoge replicatie latentie. U kunt dit probleem oplossen door het [langzame query logboek](concepts-server-logs.md) op de replica server in te scha kelen.

Trage query's kunnen het Resource verbruik verhogen of de server vertragen zodat de replica niet kan worden opgevangen met de bron server. In dit scenario kunt u de langzame query's afstemmen. Snellere query's verhinderen het blok keren van de SQL-thread en verbeteren de replicatie latentie aanzienlijk.

#### <a name="ddl-queries-on-the-source-server"></a>DDL-query's op de bron server

Op de bron server kan een Data Definition Language-opdracht (DDL), zoals [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) een lange tijd duren. Terwijl de DDL-opdracht wordt uitgevoerd, kunnen duizenden andere query's parallel worden uitgevoerd op de bron server. 

Wanneer de DDL wordt gerepliceerd, om consistentie van de data base te garanderen, voert de MySQL-engine de DDL uit in één replicatie thread. Tijdens deze taak worden alle andere gerepliceerde query's geblokkeerd en moeten wacht totdat de DDL-bewerking op de replica server is voltooid. Zelfs online DDL-bewerkingen veroorzaken deze vertraging. DDL-bewerkingen verhogen replicatie latentie.

Als u het [langzame query logboek](concepts-server-logs.md) op de bron server hebt ingeschakeld, kunt u dit latentie probleem detecteren door te controleren of er een DDL-opdracht is uitgevoerd op de bron server. Via het weghalen van de index, het wijzigen van de naam en het maken van, kunt u het InPlace-algoritme voor de ALTER TABLE gebruiken. Mogelijk moet u de tabel gegevens kopiëren en de tabel opnieuw samen stellen.

Normaal gesp roken wordt gelijktijdige DML ondersteund voor het InPlace-algoritme. U kunt echter kort een exclusieve meta gegevens vergrendeling voor de tabel nemen wanneer u de bewerking voorbereidt en uitvoert. Voor de instructie CREATE INDEX kunt u de-componenten ALGORITHM en LOCK gebruiken om de methode voor het kopiëren van tabellen en het niveau van gelijktijdigheid voor lezen en schrijven te beïnvloeden. U kunt nog steeds DML-bewerkingen voor komen door een index met volledige tekst of ruimtelijke index toe te voegen.

In het volgende voor beeld wordt een index gemaakt met behulp van de ALGORITME-en LOCK-componenten.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Voor een DDL-instructie waarvoor een vergren deling is vereist, kunt u de replicatie latentie niet voor komen. Als u de mogelijke gevolgen wilt beperken, kunt u deze typen DDL-bewerkingen uitvoeren tijdens daluren.

#### <a name="downgraded-replica-server"></a>Downgrade-replica server

In Azure Database for MySQL worden bij het lezen van replica's dezelfde server configuratie gebruikt als voor de bron server. U kunt de configuratie van de replica server wijzigen nadat deze is gemaakt.

Als de replica server downgrade heeft uitgevoerd, kan de werk belasting meer resources verbruiken, die op zijn beurt kunnen leiden tot replicatie latentie. Als u dit probleem wilt detecteren, gebruikt u Azure Monitor om het CPU-en geheugen gebruik van de replica server te controleren.

In dit scenario raden we u aan de configuratie van de replica server te laten staan op waarden die gelijk zijn aan of groter zijn dan de waarden van de bron server. Met deze configuratie kan de replica worden bewaard bij de bron server.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>Replicatie latentie verbeteren door de para meters van de bron server te optimaliseren

In Azure Database for MySQL is replicatie standaard geoptimaliseerd om te worden uitgevoerd met parallelle threads op replica's. Wanneer hoge-gelijktijdigheids workloads op de bron server ertoe leiden dat de replica server achterblijft, kunt u de replicatie latentie verbeteren door het configureren van de para meter binlog_group_commit_sync_delay op de bron server.

De binlog_group_commit_sync_delay para meter bepaalt hoeveel micro seconden het binaire logboek door voeren wacht voordat het binaire logboek bestand wordt gesynchroniseerd. Het voor deel van deze para meter is dat in plaats van onmiddellijk elke vastgelegde trans actie wordt toegepast, de bron server de binaire logboek updates bulksgewijs verzendt. Deze vertraging vermindert de i/o van de replica en helpt de prestaties te verbeteren.

Het kan handig zijn om de binlog_group_commit_sync_delay-para meter in te stellen op 1000. Controleer vervolgens de replicatie latentie. Stel deze para meter voorzichtig in en gebruik deze alleen voor werk belastingen met hoge gelijktijdigheid.

> [!IMPORTANT]
> In replica server wordt binlog_group_commit_sync_delay para meter aanbevolen als 0. Dit wordt aanbevolen omdat de replica server in tegens telling tot de bron server geen hoge gelijktijdigheid heeft en de waarde voor binlog_group_commit_sync_delay op de replica server onbedoeld kan leiden tot een verhoging van de replicatie vertraging.

Bij een laag gelijktijdigheids werk belasting die veel Singleton-trans acties bevat, kan de binlog_group_commit_sync_delay-instelling de latentie verhogen. De latentie kan toenemen omdat de IO-thread wacht op bulk binaire logboek updates, zelfs als er slechts enkele trans acties worden doorgevoerd.

## <a name="next-steps"></a>Volgende stappen

Bekijk het overzicht van de [MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
