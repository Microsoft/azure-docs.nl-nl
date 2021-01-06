---
title: Apache Kafka verbinding maken op Azure Event Hubs integreren met Debezium voor Change Data Capture
description: Dit artikel bevat informatie over het gebruik van Debezium met Azure Event Hubs voor Kafka.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 01/06/2021
ms.openlocfilehash: 0ad1df23e71e652f7d380ffbabb542b81954e038
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935169"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-with-debezium-for-change-data-capture"></a>Apache Kafka Connect-ondersteuning op Azure Event Hubs integreren met Debezium voor Change Data Capture

**Change Data Capture (CDC)** is een techniek die wordt gebruikt voor het bijhouden van wijzigingen op rijniveau in database tabellen in reactie op Create-, update-en delete-bewerkingen. [Debezium](https://debezium.io/) is een gedistribueerd platform dat is gebaseerd op wijzigingen in de functies voor het vastleggen van gegevens die beschikbaar zijn in verschillende data bases (bijvoorbeeld [logische decodering in postgresql](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)). Het bevat een set [Kafka Connect-connectors](https://debezium.io/documentation/reference/1.2/connectors/index.html) die op wijzigingen in rijen op rijniveau tikken in database tabellen (en) en deze converteren naar gebeurtenis stromen die vervolgens naar [Apache Kafka](https://kafka.apache.org/)worden verzonden.

> [!WARNING]
> Het gebruik van de Apache Kafka Connect Framework en de Debezium-platform en de bijbehorende connectors **komen niet in aanmerking voor product ondersteuning via Microsoft Azure**.
>
> Apache Kafka Connect gaat ervan uit dat de dynamische configuratie wordt opgeslagen in gecomprimeerde onderwerpen met een andere onbeperkte Bewaar periode. Azure Event Hubs [implementeert geen compressie als een Broker-functie](event-hubs-federation-overview.md#log-projections) en neemt altijd een Bewaar limiet op basis van tijd op voor behouden gebeurtenissen, in het hoofd van het principe dat Azure Event hubs een real-time engine voor gebeurtenis streaming is en geen gegevens-of configuratie opslag op lange termijn.
>
> Hoewel het Apache Kafka-project mogelijk vertrouwd is met het combi neren van deze rollen, is Azure van mening dat dergelijke informatie het beste kan worden beheerd in een juiste data base of configuratie opslag.
>
> Veel Apache Kafka verbindings scenario's werken wel, maar deze conceptuele verschillen tussen de Bewaar modellen van Apache Kafka en Azure Event Hubs kunnen ertoe leiden dat bepaalde configuraties niet naar behoren werken. 

In deze zelf studie leert u hoe u een op change data capture gebaseerd systeem op Azure kunt instellen met behulp van [azure Event hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (voor Kafka), [Azure DB voor postgresql](../postgresql/overview.md) en Debezium. De [Debezium postgresql-connector](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) wordt gebruikt voor het streamen van database wijzigingen van postgresql naar Kafka-onderwerpen in azure Event hubs

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *whitelist*, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Azure Database for PostgreSQL instellen en configureren
> * Kafka Connect met Debezium PostgreSQL-connector configureren en uitvoeren
> * change data capture testen
> * Beschrijving Change Data-gebeurtenissen gebruiken met een `FileStreamSink` connector

## <a name="pre-requisites"></a>Vereisten
Als u deze procedure wilt volt ooien, hebt u het volgende nodig:

- Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/).
- Linux/Mac OS
- Kafka-release (versie 1.1.1, Scala-versie 2.11), beschikbaar op [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Lees het inleidende artikel [Event Hubs voor Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md) door

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Er is een Event Hubs-naamruimte vereist om gegevens te verzenden naar en te ontvangen van Event Hubs-services. Zie [een event hub maken](event-hubs-create.md) voor instructies voor het maken van een naam ruimte en een event hub. Haal de Event Hubs-verbindingsreeks en de Fully Qualified Domain Name (FQDN) op voor later gebruik. Zie [Get an Event Hubs connection string](event-hubs-get-connection-string.md). (Een Event Hubs-verbindingsreeks ophalen). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Azure Database for PostgreSQL instellen en configureren
[Azure database for PostgreSQL](../postgresql/overview.md) is een relationele database service op basis van de Community-versie van open-source PostgreSQL data base engine en is beschikbaar in twee implementatie opties: één server en grootschalige (Citus). [Volg deze instructies](../postgresql/quickstart-create-server-database-portal.md) om een Azure database for postgresql server te maken met behulp van de Azure Portal. 

## <a name="setup-and-run-kafka-connect"></a>Kafka-verbinding instellen en uitvoeren
In deze sectie komen de volgende onderwerpen aan bod:

- Installatie van Debezium-connector
- Kafka Connect configureren voor Event Hubs
- Kafka Connect-cluster met Debezium-connector starten

### <a name="download-and-setup-debezium-connector"></a>Debezium-connector downloaden en instellen
Volg de laatste instructies in de [Debezium-documentatie](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) om de connector te downloaden en in te stellen.

- Down load het invoeg toepassings archief van de connector. Als u bijvoorbeeld `1.2.0` de versie van de connector wilt downloaden, gebruikt u deze koppeling: https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- Pak de JAR-bestanden uit en kopieer deze naar de [Kafka Connect-invoeg toepassing. pad](https://kafka.apache.org/documentation/#connectconfigs).


### <a name="configure-kafka-connect-for-event-hubs"></a>Kafka Connect configureren voor Event Hubs
Er is een minimale herconfiguratie vereist als u doorvoer van Kafka Connect wilt omleiden van Kafka naar Event Hubs.  In het volgende `connect-distributed.properties`-voorbeeld wordt getoond hoe u Connect kunt configureren om op Event Hubs het Kafka-eindpunt te verifiëren en hoe ermee te communiceren:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>Kafka Connect uitvoeren
In deze stap wordt een Kafka Connect-werkrol lokaal wordt gestart in gedistribueerde modus, waarbij Event Hubs wordt gebruikt om de clusterstatus te handhaven.

1. Sla het bovenstaande `connect-distributed.properties`-bestand lokaal op.  Vervang alle waarden tussen accolades.
2. Ga naar de locatie van de Kafka-release op uw computer.
3. Voer uit `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` en wacht totdat het cluster wordt gestart.

> [!NOTE]
> Kafka Connect maakt gebruik van de Kafka AdminClient-API om automatisch onderwerpen te maken met aanbevolen configuraties, inclusief compressie. Een snelle controle van de naamruimte in de Azure-portal laat zien dat de interne onderwerpen van de Connect-werkrol automatisch zijn gemaakt.
>
> Kafka Connect interne onderwerpen **moeten gebruikmaken van compressie**.  Het Event Hubs-team is niet verantwoordelijk voor het oplossen van onjuiste configuraties als interne Connect-onderwerpen onjuist zijn geconfigureerd.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>De Debezium PostgreSQL-bron connector configureren en starten

Maak een configuratie bestand ( `pg-source-connector.json` ) voor de postgresql-bron connector, vervang de waarden door uw Azure postgresql-instantie.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` het kenmerk is een logische naam die een naam ruimte identificeert en biedt voor de specifieke PostgreSQL-database server of het cluster dat wordt bewaakt. Raadpleeg de [Debezium-documentatie](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name) voor gedetailleerde informatie.

Als u een exemplaar van de connector wilt maken, gebruikt u het Kafka Connect REST API-eind punt:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

De status van de connector controleren:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>change data capture testen
Als u change data capture in actie wilt zien, moet u records maken/bijwerken/verwijderen in de Azure PostgreSQL-data base.

Maak eerst verbinding met uw Azure PostgreSQL-data base (in het onderstaande voor beeld wordt [psql](https://www.postgresql.org/docs/12/app-psql.html)gebruikt)

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Een tabel maken en records invoegen**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

De connector moet nu actie ondernemen en wijzigings gegevens gebeurtenissen verzenden naar een Event Hubs onderwerp met de volgende nb, e `my-server.public.todos` , ervan uitgaande dat u de `my-server` waarde voor hebt `database.server.name` en `public.todos` de tabel waarvan u de wijzigingen bijhoudt (per `table.whitelist` configuratie)

**Event Hubs onderwerp controleren**

We introspect de inhoud van het onderwerp om te controleren of alles werkt zoals verwacht. In het onderstaande voor beeld wordt gebruikt [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) , maar u kunt ook [een consument maken met behulp van een van de opties die hier worden vermeld](apache-kafka-developer-guide.md)

Maak een bestand `kafkacat.conf` met de naam met de volgende inhoud:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Update `metadata.broker.list` en `sasl.password` kenmerken in de `kafkacat.conf` vorm van gegevens per Event hubs. 

Start een consument in een andere terminal:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

Als antwoord op de rijen die u zojuist hebt toegevoegd aan de tabel, ziet u de JSON-nettoladingen die de wijzigings gegevens gebeurtenissen vertegenwoordigen die zijn gegenereerd in PostgreSQL `todos` . Hier volgt een code fragment van de payload:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

De gebeurtenis bestaat uit de `payload` samen met de `schema` (weglatende voor boog). In `payload` het gedeelte ziet u hoe de bewerking maken ( `"op": "c"` ) wordt weer gegeven `"before": null` . Dit houdt in dat het een nieuwe `INSERT` rij is, met `after` waarden voor de kolommen in de rij, `source` de meta gegevens van het postgresql-exemplaar van waaruit deze gebeurtenis is opgehaald, enzovoort.

U kunt dezelfde bewerking ook uitvoeren met Update-of Delete-bewerkingen en de introspect wijzigen. Als u bijvoorbeeld de taak status wilt bijwerken voor `configure and install connector` (ervan uitgaande dat deze `id` is `3` ):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>Beschrijving FileStreamSink-connector installeren
Nu alle `todos` wijzigingen in de tabel in Event hubs onderwerp worden vastgelegd, zullen we de FileStreamSink-connector (die standaard beschikbaar is in Kafka Connect) gebruiken om deze gebeurtenissen te verbruiken.

Een configuratie bestand ( `file-sink-connector.json` ) voor de connector maken-het kenmerk vervangen door `file` uw bestands systeem

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

De connector maken en de status ervan controleren:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Data base records invoegen/bijwerken/verwijderen en de records bewaken in het geconfigureerde uitvoer Sink-bestand:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Opschonen
Kafka Connect maakt Event Hub-onderwerpen om configuraties, verschuivingen en status op te slaan die ook behouden blijven nadat het Connect-cluster is uitgeschakeld. Tenzij persistentie gewenst is, wordt het aanbevolen deze onderwerpen te verwijderen. Mogelijk wilt u ook de `my-server.public.todos` Event hub verwijderen die tijdens de uitvoering van deze procedure is gemaakt.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Event Hubs voor Kafka:  

- [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
- [Akka-streams verbinden met een Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka ontwikkelaars handleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)