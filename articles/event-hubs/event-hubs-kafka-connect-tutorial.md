---
title: Integreren met Apache Kafka Connect - Azure Event Hubs | Microsoft Docs
description: In dit artikel vindt u informatie over het gebruik van Kafka Connect with Azure Event Hubs voor Kafka.
ms.topic: how-to
ms.date: 01/06/2021
ms.openlocfilehash: f82dcdafa7921f4a994361371536b2f1ace7cbc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935152"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs"></a>Ondersteuning voor Apache Kafka Connect integreren in Azure Event Hubs
[Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) is een framework voor het maken van verbinding en het importeren/exporteren van gegevens van/naar elk extern systeem, zoals MySQL, HDFS en bestands systeem via een Kafka-cluster. Deze zelf studie begeleidt u bij het gebruik van Kafka Connect Framework met Event Hubs.

> [!WARNING]
> Het gebruik van het Apache Kafka Connect Framework en de bijbehorende connectors **komen niet in aanmerking voor product ondersteuning via Microsoft Azure**.
>
> Apache Kafka Connect gaat ervan uit dat de dynamische configuratie wordt opgeslagen in gecomprimeerde onderwerpen met een andere onbeperkte Bewaar periode. Azure Event Hubs [implementeert geen compressie als een Broker-functie](event-hubs-federation-overview.md#log-projections) en neemt altijd een Bewaar limiet op basis van tijd op voor behouden gebeurtenissen, in het hoofd van het principe dat Azure Event hubs een real-time engine voor gebeurtenis streaming is en geen gegevens-of configuratie opslag op lange termijn.
>
> Hoewel het Apache Kafka-project mogelijk vertrouwd is met het combi neren van deze rollen, is Azure van mening dat dergelijke informatie het beste kan worden beheerd in een juiste data base of configuratie opslag.
>
> Veel Apache Kafka verbindings scenario's werken wel, maar deze conceptuele verschillen tussen de Bewaar modellen van Apache Kafka en Azure Event Hubs kunnen ertoe leiden dat bepaalde configuraties niet naar behoren werken. 

In deze zelf studie wordt u begeleid bij het integreren van Kafka Connect met een Event Hub en het implementeren van basis-FileStreamSource en FileStreamSink-connectors. Deze functie is momenteel beschikbaar als preview-product. Hoewel deze connectors niet bedoeld zijn voor gebruik in productieomgevingen, laten ze een end-to-end Kafka Connect-scenario zien waarin Azure Event Hubs als een Kafka-broker fungeert.

> [!NOTE]
> Dit voor beeld is beschikbaar op [github](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Kafka Connect configureren voor Event Hubs
> * Kafka Connect uitvoeren
> * Connectors maken

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u aan de volgende vereisten voldoet om deze stappen uit te voeren:

- Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/Mac OS
- Kafka-release (versie 1.1.1, Scala-versie 2.11), beschikbaar op [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Lees het inleidende artikel [Event Hubs voor Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md) door

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Er is een Event Hubs-naamruimte vereist om gegevens te verzenden naar en te ontvangen van Event Hubs-services. Zie [een event hub maken](event-hubs-create.md) voor instructies voor het maken van een naam ruimte en een event hub. Haal de Event Hubs-verbindingsreeks en de Fully Qualified Domain Name (FQDN) op voor later gebruik. Zie [Get an Event Hubs connection string](event-hubs-get-connection-string.md). (Een Event Hubs-verbindingsreeks ophalen). 

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen
Kloon de Azure Event Hubs-opslagplaats en ga naar de submap tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Kafka Connect configureren voor Event Hubs
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


## <a name="run-kafka-connect"></a>Kafka Connect uitvoeren

In deze stap wordt een Kafka Connect-werkrol lokaal wordt gestart in gedistribueerde modus, waarbij Event Hubs wordt gebruikt om de clusterstatus te handhaven.

1. Sla het bovenstaande `connect-distributed.properties`-bestand lokaal op.  Vervang alle waarden tussen accolades.
2. Ga naar de locatie van de Kafka-release op uw computer.
4. Voer `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` uit.  De REST API van de Connect-werkrol is klaar voor interactie als u `'INFO Finished starting connectors and tasks'` ziet. 

> [!NOTE]
> Kafka Connect maakt gebruik van de Kafka AdminClient-API om automatisch onderwerpen te maken met aanbevolen configuraties, inclusief compressie. Een snelle controle van de naamruimte in de Azure-portal laat zien dat de interne onderwerpen van de Connect-werkrol automatisch zijn gemaakt.
>
>Kafka Connect interne onderwerpen **moeten gebruikmaken van compressie**.  Het Event Hubs-team is niet verantwoordelijk voor het oplossen van onjuiste configuraties als interne Connect-onderwerpen onjuist zijn geconfigureerd.

### <a name="create-connectors"></a>Connectors maken
In deze sectie wordt u begeleidt bij het starten van de FileStreamSource- en FileStreamSink-connectors. 

1. Maak een map voor gegevensbestanden voor in- en uitvoer.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Maak twee bestanden: een met seedgegevens die door de FileStreamSource-connector worden gelezen en een waarnaar de FileStreamSink-connector schrijft.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Maak een FileStreamSource-connector.  Vervang de accolades door het pad naar uw basismap.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    na het uitvoeren van de bovenstaande opdracht, ziet u de Event Hub `connect-quickstart` in uw Event Hubs-instantie.
4. Controleer de status van de bronconnector.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    U kunt eventueel [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) gebruiken om te verifiëren of er gebeurtenissen in het `connect-quickstart`-onderwerp zijn aangekomen.

5. Maak een FileStreamSink-connector.  Vervang ook hier de accolades door het pad naar uw basismap.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Controleer de status van de sinkconnector.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Verifieer of de gegevens van de bestanden zijn gerepliceerd en of de gegevens in beide bestanden identiek zijn.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Opschonen
Kafka Connect maakt Event Hub-onderwerpen om configuraties, verschuivingen en status op te slaan die ook behouden blijven nadat het Connect-cluster is uitgeschakeld. Tenzij persistentie gewenst is, wordt het aanbevolen deze onderwerpen te verwijderen. U kunt ook Event Hub `connect-quickstart` verwijderen, die tijdens deze stappen is gemaakt.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Event Hubs voor Kafka:  

- [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
- [Akka-streams verbinden met een Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka ontwikkelaars handleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)
