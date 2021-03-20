---
title: Logische replicatie en logische decodering-Azure Database for PostgreSQL-flexibele server
description: Meer informatie over het gebruik van logische replicatie en logische decodering in Azure Database for PostgreSQL-flexibele server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91707860"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Logische replicatie en logische decodering in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

De functies logische replicatie en logische decodering van PostgreSQL worden ondersteund in Azure Database for PostgreSQL-flexibele server voor post gres versie 11.

## <a name="comparing-logical-replication-and-logical-decoding"></a>Logische replicatie en logische decodering vergelijken
Logische replicatie en logische decodering hebben verschillende overeenkomsten. Beide
* u kunt gegevens uit post gres repliceren
* het [Write-Ahead logboek (Wal)](https://www.postgresql.org/docs/current/wal.html) gebruiken als de bron van wijzigingen
* Gebruik [logische replicatie sleuven](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) om gegevens te verzenden. Een sleuf vertegenwoordigt een stroom van wijzigingen.
* de [eigenschap replica Identity](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) van een tabel gebruiken om te bepalen welke wijzigingen kunnen worden verzonden
* DDL-wijzigingen niet repliceren


De twee technologieën hebben hun verschillen: logische replicatie 
* Hiermee kunt u een tabel of een set tabellen opgeven die moet worden gerepliceerd
* repliceert gegevens tussen PostgreSQL-instanties

Logische decodering 
* Hiermee worden wijzigingen geëxtraheerd in alle tabellen in een Data Base 
* kan niet rechtstreeks gegevens verzenden tussen PostgreSQL-instanties


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Vereisten voor logische replicatie en logische decodering

1. Stel de server parameter `wal_level` in op `logical` .
2. Start de server opnieuw op om de wijziging toe te passen `wal_level` .
3. Bevestig dat uw PostgreSQL-instantie netwerk verkeer van de verbinding met de bron toestaat.
4. Verleen de beheerder replicatie machtigingen voor de gebruiker.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>Logische replicatie en logische decodering gebruiken

### <a name="logical-replication"></a>Logische replicatie
Logische replicatie maakt gebruik van de termen ' Publisher ' en ' Subscriber '. 
* De uitgever is de PostgreSQL-Data Base **waaruit** u gegevens verzendt. 
* De abonnee is de PostgreSQL-Data Base **waarnaar** u gegevens verzendt.

Hier volgt een voor beeld van een code die u kunt gebruiken om logische replicatie uit te proberen.

1. Maak verbinding met de publicatiedata base. Een tabel maken en gegevens toevoegen.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Een publicatie maken voor de tabel.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Verbinding maken met de abonnee database. Maak een tabel met hetzelfde schema als op de Publisher.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Maak een abonnement dat verbinding maakt met de publicatie die u eerder hebt gemaakt.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. U kunt nu een query uitvoeren op de tabel op de abonnee. U ziet dat er gegevens van de uitgever zijn ontvangen.
   ```SQL
   SELECT * FROM basic;
   ```

U kunt meer rijen toevoegen aan de tabel van de uitgever en de wijzigingen op de abonnee bekijken.

Ga naar de PostgreSQL-documentatie voor meer informatie over [logische replicatie](https://www.postgresql.org/docs/current/logical-replication.html).

### <a name="logical-decoding"></a>Logische decodering
Logische decodering kan worden gebruikt via het streaming-protocol of de SQL-interface. 

#### <a name="streaming-protocol"></a>Streaming-Protocol
Het gebruik van wijzigingen met het streaming-protocol is vaak de voor keur. U kunt uw eigen consument/connector maken of een service van derden gebruiken, zoals [Debezium](https://debezium.io/). 

Ga naar de wal2json-documentatie voor [een voor beeld van het streaming-protocol met pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>SQL-interface 
In het onderstaande voor beeld gebruiken we de SQL-interface met de wal2json-invoeg toepassing.
 
1. Maak een sleuf.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL-opdrachten verlenen. Bijvoorbeeld:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. De wijzigingen worden verbruikt.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   De uitvoer ziet er als volgt uit:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Haal de sleuf weg nadat u deze hebt gebruikt.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Ga naar de PostgreSQL-documentatie voor meer informatie over [logische decodering](https://www.postgresql.org/docs/current/logicaldecoding.html).


## <a name="monitoring"></a>Bewaking
U moet logische decodering bewaken. Alle ongebruikte replicatie sleuven moeten worden verwijderd. De sleuven voor de post gres WAL-logboeken en relevante systeem catalogussen worden bewaard totdat de wijzigingen zijn gelezen. Als uw abonnee of consument uitvalt of niet goed is geconfigureerd, wordt uw opslag door de ongebruikte logboeken gestapeld en gevuld. Ook niet-verbruikte logboeken verhogen het risico van trans actie-ID wraparound. Beide situaties kunnen ertoe leiden dat de server niet meer beschikbaar is. Daarom is het essentieel dat logische replicatie sleuven continu worden verbruikt. Als een logische replicatie sleuf niet meer wordt gebruikt, moet u deze onmiddellijk neerzetten.

In de kolom actief in de pg_replication_slots weer gave wordt aangegeven of er een consument is verbonden met een sleuf.
```SQL
SELECT * FROM pg_replication_slots;
```

[Stel waarschuwingen](howto-alert-on-metrics.md) in voor de **Maxi maal gebruikte trans actie-Id's** en de **opslag** van flexibele server metrieken om u te waarschuwen wanneer de waarden de drempel waarde voor het vorige gemiddelde verhogen. 

## <a name="limitations"></a>Beperkingen
* **Replica's lezen** : Azure database for PostgreSQL Lees replica's worden momenteel niet ondersteund voor flexibele servers.
* **Sleuven en ha-failover** -logische replicatie sleuven op de primaire server zijn niet beschikbaar op de standby-server in uw secundaire AZ. Dit geldt voor u als op de server de optie zone-redundante hoge Beschik baarheid wordt gebruikt. In het geval van een failover naar de stand-by-server zijn logische replicatie sleuven niet beschikbaar op stand-by.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [netwerk opties](concepts-networking.md)
* Meer informatie over de [uitbrei dingen](concepts-extensions.md) die beschikbaar zijn op de flexibele server
* Meer informatie over [hoge Beschik baarheid](concepts-high-availability.md)

