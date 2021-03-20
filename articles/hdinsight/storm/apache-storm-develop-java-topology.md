---
title: Voor beeld van een Java-topologie Apache Storm-Azure HDInsight
description: Meer informatie over het maken van Apache Storm-topologieën in Java door een topologie voor een aantal woorden te maken.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020,devx-track-java
ms.date: 04/27/2020
ms.openlocfilehash: 620a4e1627b25af22db68173f35924376e26f5f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929128"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Een Apache Storm topologie maken in Java

Meer informatie over het maken van een Java-topologie voor Apache Storm. U maakt een storm-topologie die een toepassing voor het tellen van woorden implementeert. U gebruikt Apache Maven om het project te bouwen en op te pakken. Vervolgens leert u hoe u de topologie kunt definiëren met behulp van het Apache Storme stroom raamwerk.

Nadat u de stappen in dit document hebt voltooid, kunt u de topologie implementeren voor Apache Storm op HDInsight.

> [!NOTE]  
> Een voltooide versie van de Storm-topologie-voor beelden die in dit document zijn gemaakt, is beschikbaar op [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount) .

## <a name="prerequisites"></a>Vereisten

* [Java Developer Kit (JDK) versie 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache Maven](https://maven.apache.org/download.cgi) correct [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een systeem voor het bouwen van Java-projecten.

## <a name="test-environment"></a>Test omgeving

De omgeving die voor dit artikel wordt gebruikt, is een computer met Windows 10.  De opdrachten zijn uitgevoerd in een opdracht prompt en de verschillende bestanden zijn bewerkt met Klad blok.

Voer vanaf een opdracht prompt de onderstaande opdrachten in om een werk omgeving te maken:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Een Maven-project maken

Voer de volgende opdracht in om een Maven-project met de naam **WordCount** te maken:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Met deze opdracht maakt u een map met `WordCount` de naam op de huidige locatie, die een Basic Maven-project bevat. Met de tweede opdracht wordt de huidige werkmap gewijzigd in `WordCount` . Met de derde opdracht maakt u een nieuwe map, `resources` die later wordt gebruikt.  De `WordCount` map bevat de volgende items:

* `pom.xml`: Bevat instellingen voor het project maven.
* `src\main\java\com\microsoft\example`: Bevat de code van uw toepassing.
* `src\test\java\com\microsoft\example`: Bevat tests voor uw toepassing.  

### <a name="remove-the-generated-example-code"></a>De gegenereerde voorbeeld code verwijderen

Verwijder de gegenereerde test-en toepassings bestanden `AppTest.java` en `App.java` Voer de volgende opdrachten in:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Maven-opslag plaatsen toevoegen

HDInsight is gebaseerd op het Hortonworks data platform (HDP), daarom raden we u aan de Hortonworks-opslag plaats te gebruiken voor het downloaden van afhankelijkheden voor uw Apache Storm-projecten.  

Open `pom.xml` door de volgende opdracht in te voeren:

```cmd
notepad pom.xml
```

Voeg vervolgens de volgende XML toe na de `<url>https://maven.apache.org</url>` regel:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Eigenschappen toevoegen

Met maven kunt u waarden op project niveau met de naam eigenschappen definiëren. `pom.xml`Voeg in de volgende tekst toe na de `</repositories>` regel:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

U kunt deze waarde nu in andere secties van de gebruiken `pom.xml` . Wanneer u bijvoorbeeld de versie van Storm-onderdelen opgeeft, kunt u `${storm.version}` in plaats van een waarde vaste code ring gebruiken.

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

Een afhankelijkheid voor Storm-onderdelen toevoegen. `pom.xml`Voeg in de sectie de volgende tekst toe `<dependencies>` :

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Tijdens het compileren gebruikt maven deze informatie om op te zoeken `storm-core` in de Maven-opslag plaats. Het controleert eerst op de opslag plaats op uw lokale computer. Als de bestanden niet aanwezig zijn, worden ze door maven gedownload uit de open bare maven-opslag plaats en opgeslagen in de lokale opslag plaats.

> [!NOTE]  
> Let `<scope>provided</scope>` op de regel in deze sectie. Met deze instelling wordt aangegeven dat maven **Storm-kern** moet worden uitgesloten van alle JAR-bestanden die worden gemaakt, omdat deze worden verschaft door het systeem.

## <a name="build-configuration"></a>Configuratie compileren

Met maven-invoeg toepassingen kunt u de build-fasen van het project aanpassen. Bijvoorbeeld hoe het project wordt gecompileerd of hoe het moet worden ingepakt in een JAR-bestand. `pom.xml`Voeg in de volgende tekst toe boven de `</project>` regel.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Deze sectie wordt gebruikt om invoeg toepassingen, resources en andere configuratie opties voor het maken van een toepassing toe te voegen. Zie voor een volledige referentie van het `pom.xml` bestand [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html) .

### <a name="add-plug-ins"></a>Invoeg toepassingen toevoegen

* **Exec maven-invoeg toepassing**

    Voor Apache Storm topologieën die in Java zijn geïmplementeerd, is de [exec maven-invoeg toepassing](https://www.mojohaus.org/exec-maven-plugin/) handig omdat u de topologie eenvoudig lokaal kunt uitvoeren in uw ontwikkel omgeving. Voeg het volgende toe aan de `<plugins>` sectie van het `pom.xml` bestand voor het toevoegen van de exec maven-invoeg toepassing:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Apache Maven compiler-invoeg toepassing**

    Een andere handige invoeg toepassing is de [`Apache Maven Compiler Plugin`](https://maven.apache.org/plugins/maven-compiler-plugin/) , die wordt gebruikt om de compilatie opties te wijzigen. Wijzig de Java-versie die maven gebruikt voor de bron en het doel voor uw toepassing.

  * Voor HDInsight __3,4 of eerder__ stelt u de bron-en doel-Java-versie in op __1,7__.

  * Voor HDInsight __3,5__ stelt u de bron-en doel-Java-versie in op __1,8__.

  Voeg de volgende tekst toe `<plugins>` aan de sectie van het bestand, waarin `pom.xml` de invoeg toepassing Apache Maven compiler wordt vermeld. In dit voor beeld wordt 1,8 opgegeven, dus is de HDInsight-versie van het doel 3,5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Resources configureren

In het gedeelte resources kunt u niet-code resources toevoegen, zoals configuratie bestanden die nodig zijn voor onderdelen in de topologie. Voor dit voor beeld voegt u de volgende tekst toe aan de `<resources>` sectie van het `pom.xml` bestand. Sla het bestand vervolgens op en sluit het.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

In dit voor beeld wordt de map resources in de hoofdmap van het project ( `${basedir}` ) toegevoegd als een locatie die bronnen bevat, en het bestand met de naam `log4j2.xml` . Dit bestand wordt gebruikt om te configureren welke gegevens worden geregistreerd door de topologie.

## <a name="create-the-topology"></a>De topologie maken

Een op Java gebaseerde Apache Storm topologie bestaat uit drie onderdelen die u als een afhankelijkheid (of referentie) moet maken.

* **Spouts**: gegevens uit externe bronnen worden gelezen en gegevens stromen in de topologie worden verzonden.

* **Bouten**: verwerkt stromen op basis van spouts of andere schichten en levert een of meer streams.

* **Topologie**: definieert hoe de spouts en schichten worden gerangschikt en biedt het toegangs punt voor de topologie.

### <a name="create-the-spout"></a>De Spout maken

Om de vereisten voor het instellen van externe gegevens bronnen te reduceren, worden in de volgende Spout alleen wille keurige zinnen gegeven. Het is een gewijzigde versie van een Spout die wordt meegeleverd met de [Storm-starter-voor beelden](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Hoewel voor deze topologie één Spout wordt gebruikt, kunnen andere gegevens van verschillende bronnen in de topologie worden gefeedd.`.`

Voer de onderstaande opdracht in om een nieuw bestand te maken en te openen `RandomSentenceSpout.java` :

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Kopieer en plak de Java-code hieronder in het nieuwe bestand.  Sluit het bestand.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Zie een van de volgende voor beelden voor een voor beeld van een Spout dat van een externe gegevens bron wordt gelezen:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): een voor beeld van een Spout die van Twitter wordt gelezen.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): een Spout die van Kafka wordt gelezen.

### <a name="create-the-bolts"></a>De bouten maken

Bouten verwerken de gegevens verwerking. Schichten kunnen alles doen, bijvoorbeeld berekening, persistentie of praten met externe onderdelen. In deze topologie worden twee schichten gebruikt:

* **SplitSentence**: splitst de zinnen die worden verzonden door **RandomSentenceSpout** in afzonderlijke woorden.

* **WordCount**: telt het aantal keren dat elk woord heeft plaatsgevonden.

#### <a name="splitsentence"></a>SplitSentence

Voer de onderstaande opdracht in om een nieuw bestand te maken en te openen `SplitSentence.java` :

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Kopieer en plak de Java-code hieronder in het nieuwe bestand.  Sluit het bestand.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Voer de onderstaande opdracht in om een nieuw bestand te maken en te openen `WordCount.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Kopieer en plak de Java-code hieronder in het nieuwe bestand.  Sluit het bestand.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>De topologie definiëren

De topologie verbindt de spouts en schichten samen in een grafiek. De grafiek definieert hoe gegevens stromen tussen de onderdelen. Het biedt ook parallellisme-hints die storm gebruiken bij het maken van exemplaren van de onderdelen in het cluster.

De volgende afbeelding is een basis diagram van de grafiek van onderdelen voor deze topologie.

![diagram van de spouts-en bouten-indeling](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Als u de topologie wilt implementeren, voert u de onderstaande opdracht in om een nieuw bestand te maken en te openen `WordCountTopology.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Kopieer en plak de Java-code hieronder in het nieuwe bestand.  Sluit het bestand.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Logboek registratie configureren

Storm gebruikt [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) om informatie te registreren. Als u geen logboek registratie configureert, verzendt de topologie diagnostische gegevens. Als u wilt bepalen wat er wordt geregistreerd, maakt u een bestand met de naam `log4j2.xml` in de `resources` map door de volgende opdracht in te voeren:

```cmd
notepad resources\log4j2.xml
```

Kopieer en plak de onderstaande XML-tekst in het nieuwe bestand.  Sluit het bestand.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Met deze XML wordt een nieuwe logboek registratie voor de `com.microsoft.example` klasse geconfigureerd, waaronder de onderdelen in deze voorbeeld topologie. Het niveau is ingesteld op tracering voor deze logger, waarmee logboek gegevens worden vastgelegd die worden gegenereerd door onderdelen in deze topologie.

De `<Root level="error">` sectie configureert het basis niveau van logboek registratie (alles niet in `com.microsoft.example` ) om alleen fout gegevens te registreren.

Zie voor meer informatie over het configureren van logboek registratie voor Log4j 2 [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html) .

> [!NOTE]  
> Storm-versie 0.10.0 en hoger gebruiken Log4j 2. x. Oudere versies van Storm gebruiken Log4j 1. x, die een andere indeling hebben gebruikt voor de logboek configuratie. Zie voor meer informatie over de oudere configuratie [https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat](https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat) .

## <a name="test-the-topology-locally"></a>De topologie lokaal testen

Nadat u de bestanden hebt opgeslagen, gebruikt u de volgende opdracht om de topologie lokaal te testen.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Terwijl deze wordt uitgevoerd, wordt in de topologie opstart gegevens weer gegeven. De volgende tekst is een voor beeld van de uitvoer van het aantal woorden:

```output
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow
```

In dit voorbeeld logboek wordt aangegeven dat het woord ' en ' 113 keer is verzonden. Het aantal blijft toenemen, zolang de topologie wordt uitgevoerd. Deze toename is omdat de Spout continu dezelfde zinnen verzendt.

Er is een interval van vijf seconden tussen de emissie van woorden en tellingen. Het **WordCount** -onderdeel is zo geconfigureerd dat alleen gegevens worden verzonden wanneer een Tick tuple arriveert. Er wordt gevraagd dat Tick-Tuples elke vijf seconden worden geleverd.

## <a name="convert-the-topology-to-flux"></a>De topologie omzetten naar stroom

[Stroom](https://storm.apache.org/releases/2.0.0/flux.html) is een nieuw Framework dat beschikbaar is met Storm 0.10.0 en hoger. Met stroom kunt u de configuratie van de implementatie scheiden. Uw onderdelen zijn nog in Java gedefinieerd, maar de topologie wordt gedefinieerd met behulp van een YAML-bestand. U kunt een standaard topologie definitie met uw project inpakken of een zelfstandig bestand gebruiken bij het verzenden van de topologie. Wanneer de topologie wordt verzonden naar Storm, gebruikt u omgevings variabelen of configuratie bestanden om de YAML-topologie definitie waarden in te vullen.

Het YAML-bestand definieert de onderdelen die moeten worden gebruikt voor de topologie en de gegevens stroom ertussen. U kunt een YAML-bestand opnemen als onderdeel van het jar-bestand. U kunt ook een extern YAML-bestand gebruiken.

Zie voor meer informatie over stroom [stroom Framework ( https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Als gevolg van een [bug https://issues.apache.org/jira/browse/STORM-2055) (](https://issues.apache.org/jira/browse/STORM-2055) met storm 1.0.1 moet u mogelijk een storm- [ontwikkel omgeving](https://storm.apache.org/releases/current/Setting-up-development-environment.html) installeren om de stroom topologie lokaal uit te voeren.

1. Voorheen, `WordCountTopology.java` de topologie gedefinieerd, maar is niet nodig met stroom. Verwijder het bestand met de volgende opdracht:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Voer de onderstaande opdracht in om een nieuw bestand te maken en te openen `topology.yaml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    Kopieer en plak vervolgens de onderstaande tekst in het nieuwe bestand.  Sluit het bestand.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Voer de onderstaande opdracht in om `pom.xml` de hieronder beschreven revisies te openen:

    ```cmd
    notepad pom.xml
    ```

   1. Voeg de volgende nieuwe afhankelijkheden toe aan de `<dependencies>` sectie:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Voeg de volgende invoeg toepassing toe aan de `<plugins>` sectie. Deze invoeg toepassing verwerkt het maken van een pakket (jar-bestand) voor het project en past enkele trans formaties toe die specifiek zijn voor stroom bij het maken van het pakket.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   1. Voor de sectie exec maven-invoeg toepassing gaat u naar `<configuration>`  >  `<mainClass>` en wijzigt `${storm.topology}` u in `org.apache.storm.flux.Flux` . Met deze instelling kan stroom worden verwerkt om de topologie lokaal uit te voeren in de ontwikkeling.

   1. Voeg in de `<resources>` sectie het volgende toe aan `<includes>` . Deze XML bevat het YAML-bestand dat de topologie definieert als onderdeel van het project.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>De stroom topologie lokaal testen

1. Voer de volgende opdracht in om de stroom topologie te compileren en uit te voeren met maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Als uw topologie Storm 1.0.1-bits gebruikt, mislukt deze opdracht. Deze fout wordt veroorzaakt door [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) . Installeer in plaats daarvan [Storm in uw ontwikkel omgeving](https://storm.apache.org/releases/current/Setting-up-development-environment.html) en gebruik de volgende stappen:
    >
    > Als u [Storm hebt geïnstalleerd in uw ontwikkel omgeving](https://storm.apache.org/releases/current/Setting-up-development-environment.html), kunt u in plaats daarvan de volgende opdrachten gebruiken:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local`Met de para meter wordt de topologie in de lokale modus uitgevoerd op uw ontwikkel omgeving. De `-R /topology.yaml` para meter gebruikt de `topology.yaml` Bestands resource uit het jar-bestand om de topologie te definiëren.

    Terwijl deze wordt uitgevoerd, wordt in de topologie opstart gegevens weer gegeven. De volgende tekst is een voor beeld van de uitvoer:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Er is een vertraging van 10 seconden tussen batches van geregistreerde gegevens.

2. Een nieuwe topologie yaml maken op basis van het project.

    1. Voer de onderstaande opdracht in om te openen `topology.xml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Zoek de volgende sectie en wijzig de waarde van `10` in `5` . Deze wijziging wijzigt het interval tussen het verzenden van batches van woorden van 10 seconden naar 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Bestand opslaan als `newtopology.yaml` .

3. Voer de volgende opdracht in om de topologie uit te voeren:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Of, als u Storm hebt in uw ontwikkelings omgeving:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Met deze opdracht wordt de `newtopology.yaml` topologie definitie gebruikt. Omdat we de para meter niet hebben opgenomen `compile` , gebruikt maven de versie van het project dat in de vorige stappen is gemaakt.

    Zodra de topologie is gestart, ziet u dat de tijd tussen verzonden batches is gewijzigd, zodat deze overeenkomt met de waarde in `newtopology.yaml` . Zo kunt u zien dat u uw configuratie kunt wijzigen via een YAML-bestand zonder dat u de topologie opnieuw hoeft te compileren.

Zie voor meer informatie over deze en andere functies van het stroom raamwerk [stroom ( https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) is een abstracte abstractie op hoog niveau die wordt verschaft door storm. Het ondersteunt stateful verwerking. Het belangrijkste voor deel van Trident is dat het ervoor zorgt dat elk bericht dat de topologie binnengaat, slechts één keer wordt verwerkt. Zonder gebruik van Trident kan uw topologie alleen garanderen dat berichten ten minste één keer worden verwerkt. Er zijn ook andere verschillen, zoals ingebouwde onderdelen die kunnen worden gebruikt in plaats van bouten te maken. Schichten worden vervangen door minder algemene onderdelen, zoals filters, prognoses en functies.

Trident-toepassingen kunnen worden gemaakt met behulp van Maven-projecten. U gebruikt dezelfde basis stappen zoals eerder in dit artikel wordt weer gegeven. alleen de code wijkt af. Trident kan ook niet (momenteel) worden gebruikt met het stroom kader.

Zie de [TRIDENT API overview](https://storm.apache.org/releases/current/Trident-API-Overview.html)(Engelstalig) voor meer informatie over Trident.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een Apache Storm topologie maakt met behulp van Java. Lees nu het volgende:

* [Apache Storm topologieën implementeren en beheren in HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Topologieën ontwikkelen met Python](apache-storm-develop-python-topology.md)

U vindt meer voorbeeld Apache Storm topologieën door te bezoeken [voorbeeld topologieën voor Apache Storm op HDInsight](apache-storm-example-topology.md).