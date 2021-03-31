---
title: Apache Kafka en Azure Cosmos DB Cassandra-API integreren met behulp van Kafka Connect
description: Meer informatie over het opnemen van gegevens van Kafka naar Azure Cosmos DB Cassandra-API met DataStax Apache Kafka connector
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803626"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Gegevens opnemen van Apache Kafka in Azure Cosmos DB Cassandra-API met behulp van Kafka Connect
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Bestaande Cassandra-toepassingen kunnen gemakkelijk samen werken met de [Azure Cosmos DB Cassandra-API](cassandra-introduction.md) als gevolg van de compatibiliteit van het [CQLv4-stuur programma](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). U maakt gebruik van deze mogelijkheid om te integreren met streaming-platformen, zoals [Apache Kafka](https://kafka.apache.org/) en gegevens in azure Cosmos DB te brengen.

Gegevens in Apache Kafka (onderwerpen) zijn alleen nuttig wanneer ze door andere toepassingen worden gebruikt of in andere systemen worden opgenomen. Het is mogelijk om een oplossing te bouwen met behulp van de [Kafka producer/Consumer](https://kafka.apache.org/documentation/#api) api's [met behulp van de taal-en client-SDK van uw keuze](https://cwiki.apache.org/confluence/display/KAFKA/Clients). Kafka Connect biedt een alternatieve oplossing. Het is een platform voor het streamen van gegevens tussen Apache Kafka en andere systemen op een schaal bare en betrouw bare manier. Omdat Kafka Connect ondersteuning biedt voor de plank connectors die Cassandra bevatten, hoeft u geen aangepaste code te schrijven om Kafka te integreren met Azure Cosmos DB Cassandra-API. 

In dit artikel wordt gebruikgemaakt van de open-source [DataStax Apache Kafka-connector](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html), die boven op Kafka Connect Framework wordt gebruikt om records van een Kafka-onderwerp op te nemen in rijen van een of meer Cassandra tabellen. Het voor beeld biedt een herbruikbare installatie met behulp van docker opstellen. Dit is heel handig omdat u hiermee alle vereiste onderdelen lokaal kunt Boots trappen met één opdracht. Deze onderdelen zijn onder andere Kafka, Zookeeper, Kafka Connect Worker en de toepassing voor het genereren van gegevens.

Hier volgt een overzicht van de onderdelen en hun service definities: u kunt naar het volledige `docker-compose` bestand [in de GitHub-opslag plaats](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml)verwijzen.

- Kafka en Zookeeper gebruiken [debezium](https://hub.docker.com/r/debezium/kafka/) -installatie kopieën.
- Als u wilt uitvoeren als docker-container, wordt de DataStax-Apache Kafka-connector geïntegreerde boven op een bestaande docker-installatie kopie- [debezium/Connect-base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Deze installatie kopie bevat een installatie van Kafka en de bijbehorende Kafka Connect-bibliotheken, waardoor het erg handig is om aangepaste connectors toe te voegen. U kunt naar de [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile)verwijzen.
- De `data-generator` service zaden wille keurig gegenereerde gegevens (JSON) in het `weather-data` Kafka-onderwerp. U kunt verwijzen naar de code en `Dockerfile` in [de GitHub-opslag plaats](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)

## <a name="prerequisites"></a>Vereisten

* [Een Azure Cosmos DB Cassandra-API-account inrichten](create-cassandra-dotnet.md#create-a-database-account)

* [Cqlsh of gehoste shell gebruiken voor validatie](cassandra-support.md#hosted-cql-shell-preview)

* [Docker](https://docs.docker.com/get-docker/) en [docker opstellen](https://docs.docker.com/compose/install) installeren

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Maak een spatie, tabellen en start de integratie pijplijn

Maak met behulp van de Azure Portal de Cassandra-en de vereiste tabellen voor de demo toepassing.

> [!NOTE]
> Gebruik dezelfde spatie-en tabel namen als hieronder

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

De GitHub-opslag plaats klonen:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Alle services starten:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> Het kan even duren voordat de containers zijn gedownload en gestart: dit is slechts één keer proces.

Controleren of alle containers zijn gestart:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

De gegevens Generator toepassing begint met de pomp gegevens in het `weather-data` onderwerp in Kafka. U kunt ook snelle Sanity-controle uitvoeren om te bevestigen. Inzoomen op de docker-container met de Kafka Connect Worker:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Als u de container shell hebt ingehaald, start u gewoon het gebruikelijke Kafka-console proces en ziet u de weers gegevens (in JSON-indeling) in de stroom.

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Cassandra Sink-connector instellen

Kopieer de inhoud van de JSON hieronder naar een bestand (u kunt dit een naam gegeven `cassandra-sink-config.json` ). U moet deze bijwerken volgens uw installatie, en in de rest van deze sectie vindt u meer informatie over dit onderwerp.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Hier volgt een overzicht van de kenmerken:

**Basis connectiviteit**

- `contactPoints`: Voer het contact punt in voor Cosmos DB Cassandra
- `loadBalancing.localDc`: Geef de regio voor Cosmos DB account op, bijvoorbeeld Zuidoost-Azië
- `auth.username`: Voer de gebruikers naam in
- `auth.password`: Voer het wacht woord in
- `port`: Geef de poort waarde op (dit is `10350` niet `9042` . laten staan)

**SSL-configuratie**

Azure Cosmos DB afdwingt [beveiligde connectiviteit via SSL](database-security.md) en Kafka Connect connector biedt ook ondersteuning voor SSL.

- `ssl.keystore.path`: pad naar de JDK-sleutel opslag in de container- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: JDK-opslag wachtwoord (standaard)
- `ssl.hostnameValidation`: De validatie van de hostnaam van het knoop punt wordt ingeschakeld
- `ssl.provider`: `JDK` wordt gebruikt als de SSL-provider

**Algemene para meters**

- `key.converter`: We gebruiken het teken reeks conversie programma `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: omdat de gegevens in Kafka-onderwerpen JSON zijn, maken we gebruik van `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`: Aangezien aan de JSON-Payload geen schema is gekoppeld (voor het doel van de demo-app), moeten we ervoor zorgen dat Kafka verbinding maken om een schema niet te zoeken door dit kenmerk in te stellen op `false` . Als dat niet het geval is, leidt dit tot storingen.

### <a name="install-the-connector"></a>De connector installeren

Installeer de connector met het Kafka Connect REST-eind punt:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

De status controleren:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Als alles goed is, moet de connector het Magic-patroon beginnen. Het moet worden geverifieerd bij Azure Cosmos DB en beginnen met het opnemen van gegevens uit het onderwerp Kafka ( `weather-data` ) in Cassandra-tabellen. `weather.data_by_state``weather.data_by_station`

U kunt nu gegevens opvragen in de tabellen. Ga naar het Azure Portal, ga naar de gehoste CQL-shell voor uw Azure Cosmos DB-account.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="CQLSH openen":::

## <a name="query-data-from-azure-cosmos-db"></a>Gegevens opvragen uit Azure Cosmos DB

Controleer de `data_by_state` en- `data_by_station` tabellen. Hier volgen enkele voor beelden van query's om aan de slag te gaan:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

* [Doorvoer voor containers en databases inrichten](set-throughput.md) 
* [Best practices voor de partitie sleutel](partitioning-overview.md#choose-partitionkey)
* [Een raming van ru/s met de Azure Cosmos DB capaciteits planner](estimate-ru-with-capacity-planner.md) artikelen
