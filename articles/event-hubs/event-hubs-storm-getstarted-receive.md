---
title: 'Quickstart: Gebeurtenissen ontvangen met behulp van Apache Storm - Azure Event Hubs'
description: 'Quickstart: In dit artikel vindt u informatie over hoe u gebeurtenissen ontvangt van Azure Event Hubs met behulp van Apache Storm.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: b79c2958a10d0193b706c92a28b1792d63c24b82
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107302961"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Quickstart: Gebeurtenissen ontvangen van Event Hubs met behulp van Apache Storm

[Apache Storm](https://storm.incubator.apache.org) is een gedistribueerd realtime berekeningssysteem dat betrouwbare verwerking van niet-gebonden gegevensstromen vereenvoudigt. In deze sectie wordt beschreven hoe u een Azure Event Hubs Storm-spout gebruikt voor het ontvangen van gebeurtenissen van Event Hubs. Met Apache Storm kunt u gebeurtenissen splitsen over meerdere processen die worden gehost in verschillende knooppunten. De Event Hubs-integratie met Storm vereenvoudigt het verbruik van gebeurtenissen door de voortgang met behulp van de Zookeeper-installatie van Storm transparant te controleren, zodat u permanente controlepunten en parallelle ontvangsten van Event Hubs kunt beheren.

Zie [Overzicht van Event Hubs][Event Hubs overview] voor meer informatie over Event Hubs-ontvangstpatronen.

## <a name="prerequisites"></a>Vereisten
Voordat u met de quickstart begint, **maakt u een Event Hubs-naamruimte en een Event Hub**. Gebruik de [Azure-portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. 

## <a name="create-project-and-add-code"></a>Project maken en code toevoegen

In deze zelfstudie wordt gebruikgemaakt van een [HDInsight Storm][HDInsight Storm]-installatie, die wordt geleverd bij de Event Hubs-spout die al beschikbaar is.

1. Volg de procedure [HDInsight Storm - Aan de slag](../hdinsight/storm/apache-storm-overview.md) om een nieuw HDInsight-cluster te maken en maak er verbinding mee via Extern bureaublad.
2. Kopieer het `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar`-bestand naar uw lokale ontwikkelomgeving. Dit bevat de gebeurtenissen-storm-spout.
3. Gebruik de volgende opdracht om het pakket te installeren in het lokale Maven-archief. Zo kunt u dit in een latere stap toevoegen als een referentie in het Storm-project.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Maak in Eclipse een nieuw Maven-project (klik op **Bestand**, **Nieuw** en **Project**).
   
    ![Bestand -> Nieuw -> Project][12]
5. Selecteer **Standaardlocatie van werkruimte gebruiken** en klik vervolgens op **Volgende**
6. Selecteer het archetype **maven-archetype-quickstart** en klik op **Volgende**
7. Voeg een **GroupId** en **ArtifactId** in en klik op **Voltooien**
8. Voeg in **pom.xml** de volgende afhankelijkheden toe aan het `<dependency>`-knooppunt.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. Maak in de map **src** een bestand met de naam **Config.properties** en kopieer de volgende inhoud, die de waarden `receive rule key` en `event hub name` vervangt:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    De waarde voor **eventhub.receiver.credits** bepaalt hoeveel gebeurtenissen worden verzameld voordat ze worden vrijgegeven in de Storm-pijplijn. In dit voorbeeld wordt deze waarde ingesteld op 10, om het eenvoudig te houden. In productie moet deze waarde doorgaans hoger worden ingesteld, zoals 1024.
10. Maak een nieuwe klasse met de naam **LoggerBolt** met behulp van de volgende code:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Deze Storm-bolt legt de inhoud van de ontvangen gebeurtenissen vast. Dit kan eenvoudig worden uitgebreid om tuples op te slaan in een opslagservice. Het [HDInsight Storm met Event Hub-voorbeeld] gebruikt dezelfde benadering om gegevens op te slaan in Azure Storage en Power BI.
11. Maak een klasse met de naam **LogTopology** met de volgende code:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Met deze klasse wordt een nieuwe Event Hubs-spout gemaakt, waarbij de eigenschappen in het configuratiebestand worden gebruikt om het object te instantiëren. Het is belangrijk te weten dat in dit voorbeeld zoveel spouts-taken worden gemaakt als het aantal partities in de Event Hub, om het maximale parallellisme te gebruiken dat door die Event Hub is toegestaan.

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht][Event Hubs overview]
* [Een Event Hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.yml)

<!-- Links -->
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm met Event hub-voorbeeld]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
