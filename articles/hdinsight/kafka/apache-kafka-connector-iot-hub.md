---
title: Apache Kafka op HDInsight gebruiken met Azure IoT Hub
description: Meer informatie over het gebruik van Apache Kafka op HDInsight met Azure IoT Hub. Het Kafka Connect Azure IoT Hub project bevat een bron-en Sink-connector voor Kafka. De bron connector kan gegevens lezen van IoT Hub en de Sink-connector schrijft naar IoT Hub.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 7980003dd63e5e51d87f85542029a1f25e7223df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932872"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Apache Kafka op HDInsight gebruiken met Azure IoT Hub

Meer informatie over het gebruik van de [Apache Kafka verbinding maken met Azure IOT hub](https://github.com/Azure/toketi-kafka-connect-iothub) connector om gegevens te verplaatsen tussen Apache Kafka in HDInsight en Azure IOT hub. In dit document leert u hoe u de IoT Hub-connector kunt uitvoeren vanaf een Edge-knoop punt in het cluster.

Met de Kafka Connect API kunt u connectors implementeren waarmee continu gegevens worden opgehaald naar Kafka, of gegevens van Kafka naar een ander systeem pushen. De [Apache Kafka verbinding maken met azure IOT hub](https://github.com/Azure/toketi-kafka-connect-iothub) is een connector waarmee gegevens van Azure IOT hub worden opgehaald in Kafka. Het kan ook gegevens van Kafka naar de IoT Hub pushen.

Wanneer u van de IoT Hub haalt, gebruikt u een __bron__ connector. Wanneer u naar IoT Hub pusht, gebruikt u een __sink__ -connector. De IoT Hub-connector biedt zowel de bron-als Sink-connectors.

In het volgende diagram ziet u de gegevens stroom tussen Azure IoT Hub en Kafka op HDInsight wanneer u de-connector gebruikt.

![Afbeelding van gegevens stromen van IoT Hub naar Kafka via de connector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Zie voor meer informatie over de Connect-API [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) .

## <a name="prerequisites"></a>Vereisten

* Een Apache Kafka cluster in HDInsight. Zie de [snelstart voor Kafka in HDInsight](apache-kafka-get-started.md) voor meer informatie.

* Een Edge-knoop punt in het Kafka-cluster. Zie het document [Edge-knoop punten gebruiken met HDInsight](../hdinsight-apps-use-edge-node.md) voor meer informatie.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Een Azure-IoT Hub en-apparaat. Voor dit artikel kunt u gebruikmaken van [Connect Raspberry Pi online Simulator naar Azure IOT hub](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md).

* [Hulp programma scala build](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>De connector bouwen

1. Down load de bron voor de connector van [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) naar uw lokale omgeving.

2. Ga vanaf een opdracht prompt naar de `toketi-kafka-connect-iothub-master` map. Gebruik vervolgens de volgende opdracht om het project te bouwen en te verpakken:

    ```cmd
    sbt assembly
    ```

    Het duurt enkele minuten voordat de build is voltooid. Met de opdracht maakt u een bestand met de naam `kafka-connect-iothub-assembly_2.11-0.7.0.jar` in de `toketi-kafka-connect-iothub-master\target\scala-2.11` map voor het project.

## <a name="install-the-connector"></a>De connector installeren

1. Upload het jar-bestand naar het Edge-knoop punt van uw Kafka in HDInsight-cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` de werkelijke naam van uw cluster te vervangen. De standaard waarden voor het SSH-gebruikers account en de naam van het [Edge-knoop punt](../hdinsight-apps-use-edge-node.md#access-an-edge-node) worden hieronder gebruikt, indien nodig wijzigen.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Zodra het bestand is gekopieerd, maakt u met SSH verbinding met het Edge-knoop punt:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik de volgende opdracht om de connector te installeren in de Kafka `libs` -map:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Zorg ervoor dat uw SSH-verbinding actief is voor de resterende stappen.

## <a name="configure-apache-kafka"></a>Apache Kafka configureren

Gebruik vanuit uw SSH-verbinding met het Edge-knoop punt de volgende stappen om Kafka te configureren om de connector uit te voeren in de zelfstandige modus:

1. wachtwoordvariabele instellen. Vervang het wacht woord door het wacht woord voor het cluster en voer de volgende opdracht in:

    ```bash
    export password='PASSWORD'
    ```

1. Installeer het hulp programma [JQ](https://stedolan.github.io/jq/) . JQ maakt het gemakkelijker om JSON-documenten te verwerken die door Ambari-query's worden geretourneerd. Voer de volgende opdracht in:

    ```bash
    sudo apt -y install jq
    ```

1. Het adres van de Kafka-Brokers ophalen. Uw cluster bevat mogelijk veel Brokers, maar u hoeft alleen maar naar een of twee te verwijzen. Gebruik de volgende opdracht om het adres van twee Broker-hosts op te halen:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Kopieer de waarden voor later gebruik. De geretourneerde waarde ziet er ongeveer zo uit:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Het adres van de Apache Zookeeper-knoop punten ophalen. Er zijn verschillende Zookeeper-knoop punten in het cluster, maar u hoeft alleen maar naar een of twee te verwijzen. Gebruik de volgende opdracht om de adressen in de variabele op te slaan `KAFKAZKHOSTS` :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Wanneer de connector wordt uitgevoerd in de zelfstandige modus, `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` wordt het bestand gebruikt om te communiceren met de Kafka-brokers. Als u het `connect-standalone.properties` bestand wilt bewerken, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Voer de volgende bewerkingen uit:

    |Huidige waarde |Nieuwe waarde | Opmerking |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Vervang de `localhost:9092` waarde door de Broker-hosts uit de vorige stap|Hiermee configureert u de zelfstandige configuratie voor het Edge-knoop punt om de Kafka-Brokers te vinden.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Met deze wijziging kunt u testen met behulp van de console producent die is opgenomen in Kafka. U hebt mogelijk verschillende conversie Programma's nodig voor andere producenten en consumenten. Zie voor meer informatie over het gebruik van andere converter waarden [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Hetzelfde als hierboven.|
    |N.v.t.|`consumer.max.poll.records=10`|Toevoegen aan het einde van het bestand. Deze wijziging is het voor komen van time-outs in de Sink-connector door deze te beperken tot 10 records tegelijk. Zie [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) voor meer informatie.|

1. Als u het bestand wilt opslaan, gebruikt u __CTRL + X__, __Y__ en __voert__ u in.

1. Gebruik de volgende opdrachten om de onderwerpen te maken die door de connector worden gebruikt:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Gebruik de volgende opdracht om te controleren of de `iotin` en `iotout` -onderwerpen bestaan:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Het `iotin` onderwerp wordt gebruikt om berichten van IOT hub te ontvangen. Het `iotout` onderwerp wordt gebruikt om berichten te verzenden naar IOT hub.

## <a name="get-iot-hub-connection-information"></a>IoT Hub verbindings gegevens ophalen

Voer de volgende stappen uit om IoT hub-gegevens op te halen die worden gebruikt door de connector:

1. Haal het event hub-compatibele eind punt en de Event hub-compatibele eindpunt naam op voor uw IoT-hub. Gebruik een van de volgende methoden om deze informatie op te halen:

   * Voer __vanuit het [Azure Portal](https://portal.azure.com/)__ de volgende stappen uit:

     1. Navigeer naar uw IoT Hub en selecteer __eind punten__.
     2. Selecteer __gebeurtenissen__ van __ingebouwde eind punten__.
     3. Kopieer de waarde van de volgende velden uit __Eigenschappen__:

         * __Event hub-compatibele naam__
         * __Event hub-compatibel eind punt__
         * __Partition__

        > [!IMPORTANT]  
        > De eindpunt waarde van de portal kan extra tekst bevatten die niet nodig is in dit voor beeld. Extraheer de tekst die overeenkomt met dit patroon `sb://<randomnamespace>.servicebus.windows.net/` .

   * Gebruik __in de [Azure cli](/cli/azure/get-started-with-azure-cli)__ de volgende opdracht:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Vervang door `myhubname` de naam van uw IOT-hub. Het antwoord is vergelijkbaar met de volgende tekst:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Het __gedeelde toegangs beleid__ en de __sleutel__ ophalen. Voor dit voor beeld gebruikt u de __service__ sleutel. Gebruik een van de volgende methoden om deze informatie op te halen:

    * Voer __vanuit het [Azure Portal](https://portal.azure.com/)__ de volgende stappen uit:

        1. Selecteer __beleid voor gedeelde toegang__ en selecteer vervolgens __service__.
        2. Kopieer de waarde van de __primaire sleutel__ .
        3. Kopieer de __verbindings reeks--__ waarde voor de primaire sleutel.

    * Gebruik __in de [Azure cli](/cli/azure/get-started-with-azure-cli)__ de volgende opdracht:

        1. Als u de waarde van de primaire sleutel wilt ophalen, gebruikt u de volgende opdracht:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Vervang door `myhubname` de naam van uw IOT-hub. Het antwoord is de primaire sleutel voor het `service` beleid voor deze hub.

        2. Gebruik de volgende opdracht om de connection string voor het beleid op te halen `service` :

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Vervang door `myhubname` de naam van uw IOT-hub. Het antwoord is het connection string voor het `service` beleid.

## <a name="configure-the-source-connection"></a>De bron verbinding configureren

Als u de bron wilt configureren voor gebruik met uw IoT Hub, voert u de volgende acties uit vanaf een SSH-verbinding met het Edge-knoop punt:

1. Maak een kopie van het `connect-iot-source.properties` bestand in de `/usr/hdp/current/kafka-broker/config/` map. Als u het bestand wilt downloaden vanuit het project toketi-Kafka-Connect-iothub, gebruikt u de volgende opdracht:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Als u het `connect-iot-source.properties` bestand wilt bewerken en de gegevens van de IOT-hub wilt toevoegen, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. Zoek in de editor de volgende vermeldingen en wijzig deze:

    |Huidige waarde |Bewerken|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Vervang `PLACEHOLDER` door `iotin`. Berichten die zijn ontvangen van IoT hub, worden opgenomen in het `iotin` onderwerp.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Vervang door `PLACEHOLDER` de Event hub-compatibele naam.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Vervang door `PLACEHOLDER` het event hub-compatibele eind punt.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Vervang `PLACEHOLDER` door `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Vervang door `PLACEHOLDER` de primaire sleutel van het `service` beleid.|
    |`IotHub.Partitions=PLACEHOLDER`|Vervang door `PLACEHOLDER` het aantal partities uit de vorige stappen.|
    |`IotHub.StartTime=PLACEHOLDER`|Vervang door `PLACEHOLDER` een UTC-datum. Deze datum is wanneer de connector begint met het controleren op berichten. De datum notatie is `yyyy-mm-ddThh:mm:ssZ` .|
    |`BatchSize=100`|Vervang `100` door `5`. Deze wijziging zorgt ervoor dat de connector berichten in Kafka leest zodra er vijf nieuwe berichten zijn in IoT hub.|

    Zie [Kafka Connect source connector for Azure IOT hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)voor een voorbeeld configuratie.

1. Als u de wijzigingen wilt opslaan, gebruikt u __CTRL + X__, __Y__ en voert u vervolgens __Enter__.

Zie voor meer informatie over het configureren van de connector bron [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) .

## <a name="configure-the-sink-connection"></a>De Sink-verbinding configureren

Als u de Sink-verbinding wilt configureren voor gebruik met uw IoT Hub, voert u de volgende acties uit vanaf een SSH-verbinding met het Edge-knoop punt:

1. Maak een kopie van het `connect-iothub-sink.properties` bestand in de `/usr/hdp/current/kafka-broker/config/` map. Als u het bestand wilt downloaden vanuit het project toketi-Kafka-Connect-iothub, gebruikt u de volgende opdracht:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Als u het `connect-iothub-sink.properties` bestand wilt bewerken en de gegevens van de IOT-hub wilt toevoegen, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. Zoek in de editor de volgende vermeldingen en wijzig deze:

    |Huidige waarde |Bewerken|
    |---|---|
    |`topics=PLACEHOLDER`|Vervang `PLACEHOLDER` door `iotout`. Berichten die naar `iotout` het onderwerp worden geschreven, worden doorgestuurd naar de IOT-hub.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Vervang door `PLACEHOLDER` de Connection String voor het `service` beleid.|

    Zie [Kafka Connect Sink connector voor Azure IOT hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)voor een voorbeeld configuratie.

1. Als u de wijzigingen wilt opslaan, gebruikt u __CTRL + X__, __Y__ en voert u vervolgens __Enter__.

Zie voor meer informatie over het configureren van de connector-Sink [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="start-the-source-connector"></a>De bron connector starten

1. Als u de bron connector wilt starten, gebruikt u de volgende opdracht van een SSH-verbinding met het Edge-knoop punt:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Zodra de connector is gestart, verzendt u berichten naar IoT hub vanaf uw apparaat (s). Als de connector berichten van de IoT-hub leest en opslaat in het onderwerp Kafka, worden de gegevens in de-console geregistreerd:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Er kunnen verschillende waarschuwingen worden weer gegeven wanneer de connector wordt gestart. Deze waarschuwingen veroorzaken geen problemen met het ontvangen van berichten van de IoT-hub.

1. Stop de connector na een paar minuten door twee keer op **CTRL + C** te gebruiken. Het duurt enkele minuten voordat de connector is gestopt.

## <a name="start-the-sink-connector"></a>De Sink-connector starten

Gebruik vanaf een SSH-verbinding met het Edge-knoop punt de volgende opdracht om de Sink-connector in de zelfstandige modus te starten:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Als de connector wordt uitgevoerd, worden de gegevens weer gegeven die vergelijkbaar zijn met de volgende tekst:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> U ziet mogelijk verschillende waarschuwingen wanneer de connector wordt gestart. U kunt deze gewoon negeren.

## <a name="send-messages"></a>Berichten verzenden

Als u berichten via de connector wilt verzenden, gebruikt u de volgende stappen:

1. Open *een tweede* SSH-sessie naar het Kafka-cluster:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Het adres van de Kafka-Brokers ophalen voor de nieuwe SSH-sessie. Vervang het wacht woord door het wacht woord voor het cluster en voer de volgende opdracht in:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Als u berichten naar het `iotout` onderwerp wilt verzenden, gebruikt u de volgende opdracht:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Met deze opdracht keert u niet terug naar de normale bash-prompt. In plaats daarvan wordt de toetsenbord invoer naar het `iotout` onderwerp verzonden.

1. Als u een bericht naar uw apparaat wilt verzenden, plakt u een JSON-document in de SSH-sessie voor de `kafka-console-producer` .

    > [!IMPORTANT]  
    > U moet de waarde van de `"deviceId"` vermelding instellen op de id van uw apparaat. In het volgende voor beeld heeft het apparaat de naam `myDeviceId` :

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Het schema voor dit JSON-document wordt uitvoerig beschreven in [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

    Als u het gesimuleerde Raspberry Pi-apparaat gebruikt en het wordt uitgevoerd, wordt het volgende bericht vastgelegd door het apparaat:

    ```output
    Receive message: Turn On
    ```

    Verzend het JSON-document opnieuw, maar wijzig de waarde van de `"message"` vermelding. De nieuwe waarde wordt vastgelegd door het apparaat.

Zie voor meer informatie over het gebruik van de Sink-connector [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de Apache Kafka Connect API kunt gebruiken om de IoT Kafka-connector op HDInsight te starten. Gebruik de volgende koppelingen om andere manieren te ontdekken voor het werken met Kafka:

* [Apache Spark gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Apache Storm gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-storm-with-kafka.md)
