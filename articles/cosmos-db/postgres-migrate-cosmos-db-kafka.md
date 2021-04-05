---
title: Gegevens migreren van PostgreSQL naar Azure Cosmos DB Cassandra-API-account met behulp van Apache Kafka
description: Meer informatie over het gebruik van Kafka Connect voor het synchroniseren van gegevens van PostgreSQL naar Azure Cosmos DB Cassandra-API in realtime.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98203062"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Gegevens migreren van PostgreSQL naar Azure Cosmos DB Cassandra-API-account met behulp van Apache Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra-API in Azure Cosmos DB is een uitstekende keuze voor zakelijke workloads die worden uitgevoerd op Apache Cassandra om verschillende redenen, zoals:

* **Aanzienlijke kosten besparingen:** U kunt kosten besparen met Azure Cosmos DB, inclusief de kosten van VM'S, band breedte en eventuele toepasselijke Oracle-licenties. Bovendien hoeft u de data centers, servers, SSD-opslag, netwerken en elektriciteits kosten niet te beheren.

* **Betere schaal baarheid en beschik baarheid:** Het elimineert afzonderlijke punten aan storingen, betere schaal baarheid en beschik baarheid voor uw toepassingen.

* **Geen overhead voor beheer en controle:** Als volledig beheerde Cloud service verwijdert Azure Cosmos DB de overhead van het beheren en bewaken van een talloze instellingen.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) is een platform voor het streamen van gegevens tussen [Apache Kafka](https://kafka.apache.org/) en andere systemen op een schaal bare en betrouw bare manier. Het ondersteunt meerdere plank connectors, wat betekent dat u geen aangepaste code nodig hebt om externe systemen te integreren met Apache Kafka.

In dit artikel wordt uitgelegd hoe u een combi natie van Kafka-connectors gebruikt om een gegevens pijplijn in te stellen om voortdurend records te synchroniseren van een relationele data base, zoals [postgresql](https://www.postgresql.org/) , naar [Azure Cosmos DB Cassandra-API](cassandra-introduction.md).

## <a name="overview"></a>Overzicht

Hier volgt een globaal overzicht van de end-to-end-stroom die in dit artikel wordt weer gegeven.

Gegevens in de PostgreSQL-tabel worden gepusht naar Apache Kafka met behulp van de [Debezium postgresql-connector](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html). Dit is een Kafka Connect- **bron** connector. Het invoegen, bijwerken of verwijderen van records in de PostgreSQL-tabel wordt vastgelegd als `change data` gebeurtenissen en verzonden naar de Kafka-onderwerp (en). De [DataStax Apache Kafka-connector](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (Kafka Connect **sink** -connector), vormt het tweede deel van de pijp lijn. De wijzigings gegevens gebeurtenissen worden gesynchroniseerd vanuit het onderwerp Kafka in Azure Cosmos DB Cassandra-API tabellen.

> [!NOTE]
> Door gebruik te maken van specifieke functies van de DataStax Apache Kafka-connector kunnen we gegevens naar meerdere tabellen pushen. In dit voor beeld helpt de connector ons bij het persistent maken van wijzigingen in gegevens records in twee Cassandra-tabellen die verschillende query vereisten kunnen ondersteunen.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Cosmos DB Cassandra-API-account inrichten](create-cassandra-dotnet.md#create-a-database-account)
* [Cqlsh of gehoste shell gebruiken voor validatie](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 of hoger
* [Docker](https://www.docker.com/) (optioneel)

## <a name="base-setup"></a>Basis installatie

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Stel PostgreSQL-data base in als u dat nog niet hebt gedaan. 

Dit kan een bestaande on-premises Data Base zijn of u kunt er een op uw lokale machine [downloaden en installeren](https://www.postgresql.org/download/) . Het is ook mogelijk om een [docker-container](https://hub.docker.com/_/postgres)te gebruiken.

Een container starten:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Verbinding maken met uw PostgreSQL-exemplaar met behulp van [`psql`](https://www.postgresql.org/docs/current/app-psql.html) client:

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Een tabel maken om informatie over de voorbeeld volgorde op te slaan:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Maak met behulp van de Azure Portal de Cassandra-en de vereiste tabellen voor de demo toepassing.

> [!NOTE]
> Gebruik dezelfde spatie-en tabel namen als hieronder

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Apache Kafka instellen 

In dit artikel wordt gebruikgemaakt van een lokaal cluster, maar u kunt een andere optie kiezen. [Down load Kafka](https://kafka.apache.org/downloads), pak het uit, start het Zookeeper-en Kafka-cluster.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Connectors instellen

Installeer de Debezium-PostgreSQL en DataStax-connector voor de Apache Kafka. Down load het archief van de invoeg toepassing voor de Debezium PostgreSQL-connector. Als u bijvoorbeeld versie 1.3.0 van de connector (meest recent op het moment van schrijven) wilt downloaden, gebruikt u [deze koppeling](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Down load de DataStax Apache Kafka-connector via [deze koppeling](https://downloads.datastax.com/#akc).

Pak beide archiefmappen en kopieer de JAR-bestanden naar de [Kafka Connect-invoeg toepassing. pad](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Raadpleeg de documentatie voor [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) en [DataStax](https://docs.datastax.com/en/kafka/doc/) voor meer informatie.

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Kafka verbinding maken en gegevens pijplijn starten

### <a name="start-kafka-connect-cluster"></a>Kafka Connect-cluster starten

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>PostgreSQL-connector exemplaar starten

Sla de connector configuratie (JSON) op in een voor beeld van een bestand `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

De PostgreSQL-connector instantie starten:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> Als u dit wilt verwijderen, kunt u het volgende gebruiken: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Gegevens invoegen

De `orders_info` tabel bevat order gegevens, zoals order-id, klant-id, plaats etc. Vul de tabel met wille keurige gegevens in met behulp van de onderstaande SQL.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Er moeten 10 records in de tabel worden ingevoegd. Zorg ervoor dat u het onderstaande aantal records bijwerkt volgens `generate_series(1, 10)` uw vereisten voorbeeld, om records in te voegen `100` , te gebruiken `generate_series(1, 100)`

Bevestigen:

```bash
select * from retail.orders_info;
```

Controleer de change data capture gebeurtenissen in het onderwerp Kafka

> [!NOTE]
> Houd er rekening mee dat de naam van het onderwerp `myserver.retail.orders_info` volgens de [connector Conventie](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

De wijzigings gegevens gebeurtenissen worden weer gegeven in JSON-indeling.

### <a name="start-datastax-apache-kafka-connector-instance"></a>DataStax Apache Kafka connector-exemplaar starten

Sla de connector configuratie (JSON) op in een voor beeld van een bestand `cassandra-sink-config.json` en werk de eigenschappen bij volgens uw omgeving.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

De connector instantie starten:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

De connector moet actie ondernemen en de end-to-end-pijp lijn van PostgreSQL naar Azure Cosmos DB operationeel is.

### <a name="query-azure-cosmos-db"></a>Query Azure Cosmos DB

Controleer de Cassandra-tabellen in Azure Cosmos DB. Hier volgen enkele van de query's die u kunt proberen:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

U kunt door gaan met het invoegen van meer gegevens in PostgreSQL en bevestigen dat de records worden gesynchroniseerd met Azure Cosmos DB.

## <a name="next-steps"></a>Volgende stappen

* [Apache Kafka en Azure Cosmos DB Cassandra-API integreren met behulp van Kafka Connect](cassandra-kafka-connect.md)
* [Apache Kafka verbinding met Azure Event Hubs (preview) integreren met Debezium voor Change Data Capture](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Gegevens migreren van Oracle naar Azure Cosmos DB Cassandra-API met behulp van Blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md) 
* [Best practices voor de partitie sleutel](partitioning-overview.md#choose-partitionkey)
* [Een raming van ru/s met de Azure Cosmos DB capaciteits planner](estimate-ru-with-capacity-planner.md) artikelen

