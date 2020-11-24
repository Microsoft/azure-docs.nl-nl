---
title: Apache Kafka met Confluent-schema register in azure HDInsight
description: Implementeer een door HDInsight beheerd Kafka-cluster met een Edge-knoop punt binnen een Virtual Network en installeer vervolgens het confluente schema register op het Edge-knoop punt.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500319"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka met Confluent-schema register in azure HDInsight

Het Kafka-schema register biedt serializers die zijn aangesloten op Apache Kafka-clients die schema opslag en ophalen van Kafka-berichten die in de Avro-indeling worden verzonden. Dit wordt gebruikt als OSS-project door te hebben, maar is nu onder de [Community-licentie voor confluentiteit](https://www.confluent.io/blog/license-changes-confluent-platform/). De volgende doel einden worden ook gebruikt in het schema register:

* Schema's voor producenten en consumenten opslaan en ophalen.
* Achterwaartse/voorwaartse/Full-compatibiliteit op onderwerpen afdwingen.
* Verklein de grootte van de nettolading die naar Kafka is verzonden.

In een door HDInsight beheerd Kafka-cluster wordt het schema register doorgaans geïmplementeerd op een Edge-knoop punt om Compute-schei ding van hoofd knooppunten toe te staan.

Hieronder vindt u een representatieve architectuur van de manier waarop het schema register wordt geïmplementeerd op een HDInsight-cluster. Het schema register maakt systeem eigen een REST API voor bewerkingen op het REGI ster.  Producenten en consumenten kunnen communiceren met het schema register vanuit het VNet of met behulp van de [Kafka rest-proxy](rest-proxy.md).

![HDInsight Kafka-schema register](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Een door HDInsight beheerde Kafka implementeren met Confluent schema REGI ster

In deze sectie wordt een door HDInsight beheerd Kafka-cluster geïmplementeerd met een Edge-knoop punt in een virtueel netwerk en vervolgens het confluente schema register op het Edge-knoop punt installeren.  

1. Selecteer de onderstaande knop **implementeren naar Azure** om u aan te melden bij Azure en de Resource Manager-sjabloon te openen.

    [![Implementeren in Azure](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. Vul op de sjabloon voor aangepaste implementatie de velden in zoals hieronder wordt beschreven:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer in de vervolgkeuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster.|
    |Resourcegroep|Selecteer in de vervolgkeuzelijst de bestaande resourcegroep of selecteer **Nieuwe maken**.|
    |Region|Selecteer in de vervolgkeuzelijst een regio waarin het cluster wordt gemaakt.|
    |Clusternaam|Geef een wereldwijd unieke naam op. U kunt ook de standaard naam gebruiken.|
    |Gebruikersnaam voor clusteraanmelding|Geef de gebruikersnaam op; de standaard is **beheerder**.|
    |Wachtwoord voor clusteraanmelding|Geef het wachtwoord op.|
    |Ssh-gebruikersnaam|Geef de gebruikers naam op. de standaard waarde is **sshuser**.|
    |Ssh-wachtwoord|Geef het wachtwoord op.|

    De overige velden blijven ongewijzigd. Selecteer **Beoordelen en maken** om verder te gaan.

1. Controleer de implementatie gegevens en selecteer vervolgens **maken** om de implementatie te initialiseren. Het volt ooien van de implementatie kan 45 minuten duren.

## <a name="configure-the-confluent-schema-registry"></a>Het Confluent schema REGI ster configureren

In deze sectie configureren we het Confluent Kafka-schema register dat we hebben geïnstalleerd op het Edge-knoop punt. Het confluente schema register bevindt zich op  `/etc/schema-registry/schema-registry.properties` en de mechanismen voor het starten en stoppen van de uitvoer bare bestanden van de service bevinden zich in de  `/usr/bin/` map.

Het schema register moet weten dat de Zookeeper-service kan communiceren met het HDInsight Kafka-cluster. Volg de onderstaande stappen om de details van het Zookeeper-quorum op te halen.

1. Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met het cluster Edge-knoop punt. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. wachtwoordvariabele instellen. Vervang het wacht woord door het wacht woord voor het cluster en voer de volgende opdracht in:

    ```bash
    export password='PASSWORD'
    ```

1. Extraheer de clusternaam met de juiste letters. Voer de volgende opdracht uit:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Pak de Kafka Zookeeper-hosts uit. Voer de volgende opdracht uit:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Noteer deze waarde om deze later te gebruiken.

1. Pak de Kafka Broker-hosts uit. Voer de volgende opdracht uit:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Open de schema register eigenschappen bestanden in de bewerkings modus. Voer de volgende opdracht uit:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Werk de waarde voor `kafkastore.connection.url` met de teken reeks Zookeeper die eerder is geïdentificeerd.
    1. Werk de waarde voor `debug` naar `true` .

    Het eigenschappen bestand ziet er nu ongeveer als volgt uit:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Als u het bestand wilt opslaan, gebruikt u **CTRL + X**, **Y** en **voert** u in.

1. Start het schema register en wijs het aan om het bijgewerkte schema REGI ster-eigenschappen bestand te gebruiken. Voer de volgende opdrachten uit:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. Start een ander SSH-venster met het schema register dat wordt uitgevoerd in een SSH-sessie.  Registreer een nieuwe versie van een schema onder het onderwerp "Kafka-Key" en noteer de uitvoer:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Registreer een nieuwe versie van een schema onder het onderwerp ' Kafka-value ' en noteer de uitvoer:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Alle onderwerpen weer geven en de uitvoer controleren:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Mogelijk wilt u andere [Geavanceerde opdrachten proberen die hier worden weer gegeven](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart).

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Avro-gegevens van Kafka verzenden en gebruiken met schema register

In deze sectie worden gegevens uit de standaard invoer gelezen en naar een Kafka-onderwerp in een indeling geschreven. Vervolgens lezen we de gegevens uit de Kafka-onderwerpen met behulp van een consument met een AVRO-indelings functie om die gegevens te transformeren naar een lees bare indeling.

1. Maak een Kafka-onderwerp `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. Gebruik de **Kafka Avro-console producent** om een schema te maken, het schema toe te wijzen aan het onderwerp en te beginnen met het verzenden van gegevens naar het onderwerp in Avro-indeling. Zorg ervoor dat het onderwerp Kafka in de vorige stap is gemaakt en dat **$KAFKABROKERS** een waarde bevat.

    Het schema dat we verzenden, is een sleutel: waardepaar

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Gebruik de onderstaande opdracht om de **Kafka Avro-console producent** te starten:

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Wanneer de producent klaar is om berichten te accepteren, kunt u beginnen met het verzenden van berichten in de vooraf gedefinieerde Avro-schema-indeling. Gebruik de tab-toets om de afstand tussen de sleutel en de waarde te maken.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Probeer wille keurige niet-schema gegevens in te voeren in de console producent om te zien hoe de producent nu gegevens toestaat die niet voldoen aan het vooraf gedefinieerde Avro-schema.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. Start in een ander scherm de Kafka Avro-console gebruiker

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    U moet de onderstaande uitvoer bekijken:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Volgende stappen

* [De Apache Kafka producer-en Consumer-Api's gebruiken](apache-kafka-producer-consumer-api.md)