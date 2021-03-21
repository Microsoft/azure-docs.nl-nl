---
title: 'Zelfstudie: Apache Storm met Apache Kafka in Azure HDInsight'
description: Lees hoe u in HDInsight een streaming-pijplijn maakt met Apache Storm en Apache Kafka. In deze zelfstudie gebruikt u de componenten KafkaBolt en KafkaSpout om gegevens te streamen vanuit Kafka.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 304ba791d107ca497e65905657e87e84cdb9567d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942719"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Zelfstudie: Apache Storm gebruiken met Apache Kafka in HDInsight

Deze zelfstudie laat zien hoe u een [Apache Storm](https://storm.apache.org/)-topologie gebruikt om gegevens te lezen en te schrijven met [Apache Kafka](https://kafka.apache.org/) in HDInsight. In deze zelfstudie leert u ook hoe u gegevens opslaat in de met [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) compatibele opslag in het Storm-cluster.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Storm en Kafka
> * De code begrijpen
> * Kafka- en Storm-clusters maken
> * De topologie samenstellen
> * De topologie configureren
> * Het Kafka-onderwerp maken
> * De topologieën starten
> * De topologieën stoppen
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Kennis van Kafka-onderwerpen. Zie de [snelstart voor Kafka in HDInsight](./kafka/apache-kafka-get-started.md) voor meer informatie.

* Bekend met het maken en implementeren van Storm-oplossingen (topologieën), Met name topologieën die gebruikmaken van het [Apache Storm Flux](https://storm.apache.org/releases/current/flux.html)-framework. Zie voor meer informatie het document [Create an Apache Storm topology in Java](./storm/apache-storm-develop-java-topology.md) (Een Apache Storm-topologie maken in Java).

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) of hoger. HDInsight 3.5 of hoger vereist Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Een SSH-client (u hebt de opdrachten `ssh` en `scp` nodig) - zie [Verbinding maken met HDInsight (Hadoop) via SSH](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

U kunt de volgende omgevingsvariabelen instellen wanneer u Java en de JDK installeert op uw ontwikkelwerkstation. U moet dan echter wel controleren of ze bestaan en of ze de juiste waarden voor uw systeem bevatten.

* `JAVA_HOME` -moet verwijzen naar de map waarin de JDK is geïnstalleerd.
* `PATH` -moet de volgende paden bevatten:
  
    * `JAVA_HOME` (of het equivalente pad).
    * `JAVA_HOME\bin` (of het equivalente pad).
    * De map waarin Maven is geïnstalleerd.

> [!IMPORTANT]  
> Voor de stappen in dit document is een Azure-resourcegroep vereist die zowel een Storm in HDInsight- als een Kafka in HDInsight-cluster bevat. Deze clusters bevinden zich beide binnen een Azure Virtual Network, waardoor het Storm-cluster rechtstreeks kan communiceren met het Kafka-cluster.
> 
> Voor uw gemak is dit document gekoppeld aan een sjabloon waarmee u alle vereiste Azure-resources kunt maken. 
>
> Zie het document [Plan een virtueel netwerk voor HDInsight](hdinsight-plan-virtual-network-deployment.md) voor meer informatie over het gebruik van HDInsight in een virtueel netwerk.

## <a name="storm-and-kafka"></a>Storm en Kafka

Apache Storm biedt de verschillende onderdelen voor het werken met Apache Kafka. In deze zelfstudie worden de volgende onderdelen gebruikt:

* `org.apache.storm.kafka.KafkaSpout`: met dit onderdeel worden gegevens vanuit Kafka gelezen. Dit onderdeel is afhankelijk van de volgende onderdelen:

    * `org.apache.storm.kafka.SpoutConfig`: dit onderdeel bevat de configuratie voor het spout-onderdeel.

    * `org.apache.storm.spout.SchemeAsMultiScheme` en `org.apache.storm.kafka.StringScheme`: deze onderdelen bepalen hoe de gegevens uit Kafka worden omgezet in een Storm-tuple.

* `org.apache.storm.kafka.bolt.KafkaBolt`: met dit onderdeel worden gegevens weggeschreven naar Kafka. Dit onderdeel is afhankelijk van de volgende onderdelen:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: dit onderdeel beschrijft het onderwerp waarnaar wordt weggeschreven.

    * `org.apache.kafka.common.serialization.StringSerializer`: met dit onderdeel wordt de bolt geconfigureerd voor het serialiseren van gegevens als een tekenreekswaarde.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: met dit onderdeel worden gegevens vanuit de tuple-structuur binnen de Storm-topologie toegewezen aan velden die zijn opgeslagen in Kafka.

Deze onderdelen zijn beschikbaar in het pakket `org.apache.storm : storm-kafka`. Gebruik de pakketversie die overeenkomt met de Storm-versie. Voor HDInsight 3.6 is dit Storm versie 1.1.0.
U hebt ook het pakket `org.apache.kafka : kafka_2.10` nodig, met daarin aanvullende Kafka-onderdelen. Gebruik de pakketversie die overeenkomt met de Kafka-versie. Voor HDInsight 3.6 is dit Kafka versie 1.1.1.

De volgende XML vormt de afhankelijksdeclaratie in de `pom.xml` voor een [Apache Maven](https://maven.apache.org/)-project:

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>1.1.1</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>De code begrijpen

De code die wordt gebruikt in dit document is beschikbaar op [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Er worden twee topologieën meegeleverd in deze zelfstudie:

* Kafka-writer: genereert willekeurige zinnen en slaat deze op in Kafka.

* Kafka-reader: leest gegevens uit Kafka en slaat deze vervolgens op in het HDFS-compatibele bestandsarchief voor het Storm-cluster.

    > [!WARNING]  
    > Om het Storm-cluster te laten werken met de HDFS-compatibele opslag die wordt gebruikt door HDInsight, is een scriptactie vereist. Het script installeert verschillende jar-bestanden naar het pad `extlib` voor Storm. De sjabloon in deze zelfstudie gebruikt automatisch het script tijdens het maken van het cluster.
    >
    > Als u de sjabloon in dit document niet gebruikt om het Storm-cluster te maken, moet u de scriptactie handmatig toepassen op het cluster.
    >
    > De scriptactie bevindt zich op [https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) en wordt toegepast op de supervisor- en nimbus-knooppunten van het Storm-cluster. Zie het document [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van scriptacties](hdinsight-hadoop-customize-cluster-linux.md) voor meer informatie over het gebruik van scriptacties.

De topologieën worden gedefinieerd met [Flux](https://storm.apache.org/releases/current/flux.html). Flux werd geïntroduceerd in Storm 0.10.x en stelt u in staat om de topologieconfiguratie te scheiden van de code. In het geval van topologieën die gebruikmaken van het Flux-framework, wordt de topologie gedefinieerd in een YAML-bestand. Het YAML-bestand kan worden opgenomen als onderdeel van de topologie. Het kan ook een zelfstandig bestand zijn dat wordt gebruikt wanneer u de topologie indient. Flux ondersteunt ook het vervangen van variabelen tijdens runtime, wat we in dit voorbeeld doen.

De volgende parameters worden tijdens runtime ingesteld voor deze topologieën:

* `${kafka.topic}`: de naam van het Kafka-onderwerp waaruit/waarnaar de topologieën lezen/schrijven.

* `${kafka.broker.hosts}`: de hosts waarop de Kafka-brokers worden uitgevoerd. De broker-gegevens worden door de KafkaBolt gebruikt bij het schrijven van gegevens naar Kafka.

* `${kafka.zookeeper.hosts}`: de hosts waarop Zookeeper wordt uitgevoerd in het Kafka-cluster.

* `${hdfs.url}`: de URL van het bestandssysteem voor het HDFSBolt-onderdeel. Hiermee wordt aangegeven of de gegevens worden geschreven naar een Azure Storage-account of Azure Data Lake Storage.

* `${hdfs.write.dir}`: de map waarnaar gegevens worden weggeschreven.

Zie [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) voor meer informatie over Flux-topologieën.

### <a name="kafka-writer"></a>Kafka-writer

In de topologie voor de Kafka-writer accepteert het onderdeel voor de Kafka-bolt twee tekenreekswaarden als parameters. Deze parameters geven aan welke tuple-velden de bolt naar Kafka verstuurt als __key__- en __message__-waarden. De sleutel (key) wordt gebruikt voor het partitioneren van gegevens in Kafka. Het bericht (message) zijn de gegevens die worden opgeslagen.

In dit voorbeeld verzendt het onderdeel `com.microsoft.example.SentenceSpout` een tuple met twee velden, te weten `key` en `message`. De Kafka-bolt extraheert deze velden en verzendt de gegevens naar Kafka.

De velden hoeven niet de namen `key` en `message` te hebben. Deze namen worden in dit project gebruikt om de toewijzing gemakkelijker te begrijpen.

De volgende YAML is de definitie voor het onderdeel Kafka-writer:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-reader

In de topologie voor de Kafka-reader leest het spout-onderdeel gegevens uit Kafka als tekenreekswaarden. De gegevens worden vervolgens door het onderdeel voor logboekregistratie naar het Storm-logboek geschreven, maar ook naar het HDFS-compatibele bestandssysteem voor het Storm-cluster door het onderdeel HDFS-bolt.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Vervangen van eigenschappen

Het project bevat een bestand met de naam `dev.properties` dat wordt gebruikt voor het doorgeven van parameters die worden gebruikt door de topologieën. Het bestand bevat definities voor deze eigenschappen:

| Bestand dev.properties | Beschrijving |
| --- | --- |
| `kafka.zookeeper.hosts` | De [Apache ZooKeeper](https://zookeeper.apache.org/)-hosts voor het Kafka-cluster. |
| `kafka.broker.hosts` | De broker-hosts van Kafka (werkknooppunten). |
| `kafka.topic` | Het Kafka-onderwerp Kafka dat de topologieën gebruiken. |
| `hdfs.write.dir` | De map waarnaar de topologie voor de Kafka reader gegevens schrijft. |
| `hdfs.url` | Het bestandssysteem dat wordt gebruikt door het Storm-cluster. Gebruik voor Azure Storage-accounts de waarde `wasb://`. Gebruik voor Azure Data Lake Storage Gen2 de waarde `abfs://`. Gebruik voor Azure Data Lake Storage Gen1 de waarde `adl://`. |

## <a name="create-the-clusters"></a>De clusters maken

Apache Kafka in HDInsight biedt geen toegang tot de Kafka-brokers via het openbare internet. Alles wat gebruikmaakt van Kafka moet zich in hetzelfde Azure Virtual Network bevinden. In deze zelfstudie bevinden de Kafka- en Storm-clusters zich allebei in hetzelfde virtuele netwerk van Azure. 

Het volgende diagram laat zien hoe de communicatie tussen Storm en Kafka verloopt:

![Diagram met Storm- en Kafka-clusters in een virtueel netwerk van Azure](./media/hdinsight-apache-storm-with-kafka/apache-storm-kafka-vnet.png)

> [!NOTE]  
> Andere services in het cluster, zoals SSH en [Apache Ambari](https://ambari.apache.org/), zijn toegankelijk via internet. Zie [Poorten en URI's die worden gebruikt door HDInsight](hdinsight-hadoop-port-settings-for-services.md) voor meer informatie over de openbare poorten die beschikbaar zijn voor HDInsight.

Gebruik de volgende stappen om eerst een virtueel Azure-netwerk te maken en vervolgens de Kafka- en Storm-clusters:

1. Gebruik de volgende knop om u aan te melden bij Azure en de sjabloon in de Azure Portal te openen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    De Azure Resource Manager-sjabloon bevindt zich op **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json** . Met deze sjabloon maakt u de volgende resources:

    * Azure-resourcegroep
    * Azure Virtual Network
    * Azure Storage-account
    * Kafka in HDInsight versie 3.6 (drie werkknooppunten)
    * Storm in HDInsight versie 3.6 (drie werkknooppunten)

   > [!WARNING]  
   > Om beschikbaarheid van Kafka op HDInsight te garanderen, moet uw cluster ten minste drie werkknooppunten bevatten. Met deze sjabloon maakt u een Kafka-cluster dat drie werkknooppunten bevat.

2. Gebruik de volgende informatie voor het invullen van de velden in de sectie **Aangepaste sjabloon**:

   1. Gebruik de volgende informatie voor het vullen van de vermeldingen in de sectie **Aangepaste sjabloon**:

      | Instelling | Waarde |
      | --- | --- |
      | Abonnement | Uw Azure-abonnement |
      | Resourcegroep | De resourcegroep die de resources bevat. |
      | Locatie | De Azure-regio waarin de bronnen worden gemaakt. |
      | Naam Kafka-cluster | De naam van het Kafka-cluster. |
      | Naam Storm-cluster | De naam van het Storm-cluster. |
      | Gebruikersnaam voor clusteraanmelding | De beheerdersnaam voor de clusters. |
      | Wachtwoord voor clusteraanmelding | Het beheerderswachtwoord voor de clusters. |
      | SSH-gebruikersnaam | De SSH-gebruiker die voor de clusters wordt gemaakt. |
      | SSH-wachtwoord | Het wachtwoord voor de SSH-gebruiker. |
   
      ![Afbeelding van de sjabloonparameters](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. Lees de **voorwaarden** en schakel vervolgens het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in.

4. Schakel tot slot **Vastmaken aan dashboard** in en selecteer **Kopen**.

> [!NOTE]  
> Het kan 20 minuten duren voordat het cluster is gemaakt.

## <a name="build-the-topology"></a>De topologie samenstellen

1. Download in de ontwikkelomgeving het project op [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), open een opdrachtregel en ga naar de map waarnaar u het project hebt gedownload.

2. Gebruik in de map **hdinsight-storm-java-kafka** de volgende opdracht om het project te compileren en een pakket voor implementatie te maken:

   ```bash
   mvn clean package
   ```

    Er wordt een bestand met de naam `KafkaTopology-1.0-SNAPSHOT.jar` gemaakt in de map `target`.

3. Gebruik de volgende opdrachten om het pakket naar het Storm-cluster in HDInsight te kopiëren. Vervang `sshuser` door de SSH-gebruikersnaam voor het cluster. Vervang `stormclustername` door de naam van het __Storm__-cluster.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    Voer als hierom wordt gevraagd het wachtwoord in dat u hebt gebruikt tijdens het maken van de clusters.

## <a name="configure-the-topology"></a>De topologie configureren

1. Gebruik een van de volgende methoden om de broker-hosts van Kafka te detecteren voor het **Kafka**-cluster in HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > In he volgende Bash-voorbeeld wordt ervan uitgegaan dat `$CLUSTERNAME` de naam van het __Kafka__-cluster bevat. Ook wordt ervan uitgegaan dat [jq](https://stedolan.github.io/jq/) versie 1.5 of hoger is geïnstalleerd. Geef desgevraagd het wachtwoord voor het account voor clusteraanmelding op.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    De geretourneerde waarde ziet er ongeveer zo uit:

    ```output
    wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
     ```

    > [!IMPORTANT]  
    > Hoewel een cluster meer dan twee broker-hosts kan hebben, hoeft u niet een volledige lijst van alle hosts te leveren aan de clients. Een of twee is voldoende.

2. Gebruik een van de volgende methoden om de Zookeeper-hosts voor het __Kafka__-cluster in HDInsight te detecteren:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > In he volgende Bash-voorbeeld wordt ervan uitgegaan dat `$CLUSTERNAME` de naam van het __Kafka__-cluster bevat. Ook wordt ervan uitgegaan dat [jq](https://stedolan.github.io/jq/) is geïnstalleerd. Geef desgevraagd het wachtwoord voor het account voor clusteraanmelding op.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    De geretourneerde waarde ziet er ongeveer zo uit:

    ```output
    zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    ```

    > [!IMPORTANT]  
    > Hoewel er meer dan twee Zookeeper-knooppunten zijn, hoeft u niet een volledige lijst van alle hosts te leveren aan de clients. Een of twee is voldoende.

    Sla deze waarde op, aangezien u die later nog nodig hebt.

3. Bewerk het bestand `dev.properties` in de hoofdmap van het project. Voeg de gegevens van de Broker- en Zookeeper-hosts voor het __Kafka__-cluster toe aan de overeenkomende regels in dit bestand. Het volgende voorbeeld is geconfigureerd aan de hand van de voorbeeldwaarden uit de vorige stappen:

    ```bash
    kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
    kafka.topic: stormtopic
    ```

    > [!IMPORTANT]  
    > De vermelding `hdfs.url` is geconfigureerd voor een cluster dat gebruikmaakt van een Azure Storage-account. Als u deze topologie wilt gebruiken met een Storm-cluster dat gebruikmaakt van Data Lake Storage, wijzigt u deze waarde van `wasb` in `adl`.

4. Sla het bestand `dev.properties` op en gebruik vervolgens de volgende opdracht om het bestand te uploaden naar het **Storm**-cluster:

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Vervang **USERNAME** door de SSH-gebruikersnaam voor het cluster. Vervang **BASENAME** door de basisnaam die u hebt gebruikt bij het maken van het cluster.

## <a name="create-the-kafka-topic"></a>Het Kafka-onderwerp maken

Kafka slaat gegevens op in een _onderwerp_. U moet het onderwerp maken voordat u de Storm-topologieën gaat maken. Gebruik de volgende stappen om de topologie te maken:

1. Maak met behulp van de volgende opdracht verbinding met het __Kafka__-cluster via SSH. Vervang `sshuser` door de SSH-gebruikersnaam die u hebt gebruikt bij het maken van het cluster. Vervang `kafkaclustername` door de naam van het Kafka-cluster:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    Voer als hierom wordt gevraagd het wachtwoord in dat u hebt gebruikt tijdens het maken van de clusters.
   
    Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

2. Gebruik de volgende opdracht om het Kafka-onderwerp te maken. Vervang `$KAFKAZKHOSTS` door de gegevens van de Zookeeper-host die u hebt gebruikt bij het configureren van de topologie:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Met deze opdracht maakt u verbinding met Zookeeper voor het Kafka-cluster en maakt u een nieuw onderwerp met de naam `stormtopic`. Dit onderwerp wordt gebruikt door de Storm-topologieën.

## <a name="start-the-writer"></a>De writer starten

1. Gebruik de volgende opdracht om via SSH verbinding te maken met het **Storm**-cluster. Vervang `sshuser` door de SSH-gebruikersnaam die u hebt gebruikt bij het maken van het cluster. Vervang `stormclustername` door de naam van het Storm-cluster:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    Voer als hierom wordt gevraagd het wachtwoord in dat u hebt gebruikt tijdens het maken van de clusters.
   
    Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

2. Gebruik vanuit de SSH-verbinding met het Storm-cluster de volgende opdracht om de topologie voor de writer te starten:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    U kunt de volgende parameters gebruiken met deze opdracht:

    * `org.apache.storm.flux.Flux`: gebruik Flux om deze topologie te configureren en uit te voeren.

    * `--remote`: verstuur de topologie naar Nimbus. De topologie wordt verdeeld over de werkknooppunten in het cluster.

    * `-R /writer.yaml`: gebruik het bestand `writer.yaml` voor het configureren van de topologie. `-R` geeft aan dat deze resource wordt opgenomen in het jar-bestand. De resource bevindt zich in de hoofdmap van de jar en dus is `/writer.yaml` het pad naar de resource.

    * `--filter`: vul vermeldingen in de topologie `writer.yaml` in met behulp van de waarden in bestand `dev.properties`. Zo moet de waarde van de vermelding `kafka.topic` in het bestand worden gebruikt in plaats van de vermelding `${kafka.topic}` in de definitie van de topologie.

## <a name="start-the-reader"></a>De lezer starten

1. Gebruik vanuit de SSH-sessie met het Storm-cluster de volgende opdracht om de topologie voor de reader te starten:

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. Wacht een minuut en gebruik vervolgens de volgende opdracht om de bestanden te bekijken die zijn gemaakt door de topologie voor de reader:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    De uitvoer lijkt op het volgende:

    ```output
    Found 173 items
      -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
      -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
      -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
      ...
    ```

3. Als u de inhoud van het bestand wilt weergeven, gebruikt u de volgende opdracht. Vervang `filename.txt` door de naam van een bestand:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    De volgende tekst is een voorbeeld van de inhoud van het bestand:

    > achtenzeventig jaar geleden
    >
    > sneeuwwitje en de zeven dwergen
    >
    > Ik ben twee met de natuur
    >
    > sneeuwwitje en de zeven dwergen
    >
    > Ik ben twee met de natuur
    >
    > achtenzeventig jaar geleden
    >
    > gezond eten is belangrijk

## <a name="stop-the-topologies"></a>De topologieën stoppen

Gebruik vanuit een SSH-sessie met het Storm-cluster de volgende opdrachten om de Storm-topologieën te stoppen:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelfstudie gemaakte resources wilt opschonen, kunt u de resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende HDInsight-cluster en eventuele andere resources die aan de resourcegroep zijn gekoppeld, verwijderd.

Ga als volgt te werk om de resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies __Resourcegroepen__ om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die u wilt verwijderen en klik met de rechtermuisknop op de knop __Meer__ (... ) aan de rechterkant van de vermelding.
3. Selecteer __Resourcegroep verwijderen__ en bevestig dit.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een [Apache Storm](https://storm.apache.org/)-topologie gebruikt om gegevens te schrijven naar en te lezen uit een [Apache Kafka](https://kafka.apache.org/)-cluster in HDInsight. U hebt ook geleerd hoe u gegevens opslaat in de met [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) compatibele opslag die wordt gebruikt in HDInsight.

> [!div class="nextstepaction"]
> [Apache Kafka Producer- en Consumer-API gebruiken](kafka/apache-kafka-producer-consumer-api.md)
