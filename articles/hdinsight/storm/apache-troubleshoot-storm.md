---
title: Problemen met Storm oplossen met behulp van Azure HDInsight
description: Krijg antwoorden op veelgestelde vragen over het gebruik van Apache Storm met Azure HDInsight.
keywords: Azure HDInsight, Storm, veelgestelde vragen, gids voor probleem oplossing, veelvoorkomende problemen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: fc08adb2f71c49fad3c8574e9cfd5315f13d7f44
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95982259"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Problemen met Apache Storm oplossen met behulp van Azure HDInsight

Meer informatie over de belangrijkste problemen en hun oplossingen voor het werken met [Apache Storm](https://storm.apache.org/) Payloads in [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hoe kan ik toegang tot de Storm-gebruikers interface in een cluster?

U hebt twee opties voor het openen van de Storm-gebruikers interface vanuit een browser:

### <a name="apache-ambari-ui"></a>Apache Ambari-gebruikers interface

1. Ga naar het Ambari-dash board.
2. Selecteer **Storm** in de lijst met Services.
3. Selecteer in het menu **snelle koppelingen** **Storm-gebruikers interface**.

### <a name="direct-link"></a>Directe koppeling

U kunt de Storm-gebruikers interface openen via de volgende URL:

`https://<cluster DNS name>/stormui`

Voorbeeld: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hoe kan ik overboeking Storm Event Hub Spout van de ene topologie naar een andere?

Wanneer u topologieën ontwikkelt die vanuit Azure Event Hubs worden gelezen met behulp van het bestand HDInsight Storm Event Hub Spout. jar, moet u een topologie met dezelfde naam in een nieuw cluster implementeren. U moet echter de controlepunt gegevens bewaren die zijn vastgelegd voor [Apache ZooKeeper](https://zookeeper.apache.org/) op het oude cluster.

### <a name="where-checkpoint-data-is-stored"></a>Waar worden controlepunt gegevens opgeslagen?

Controlepunt gegevens voor offsets worden opgeslagen door de Event Hub spout in ZooKeeper in twee hoofd paden:

- Niet-transactionele Spout-controle punten worden opgeslagen in `/eventhubspout` .

- Transactionele Spout-controlepunt gegevens worden opgeslagen in `/transactional` .

### <a name="how-to-restore"></a>Herstellen

Als u de scripts en bibliotheken wilt ophalen die u gebruikt voor het exporteren van gegevens uit ZooKeeper, en vervolgens de gegevens opnieuw wilt importeren in ZooKeeper met een nieuwe naam, raadpleegt u [voor beelden van HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

De map lib heeft jar-bestanden die de implementatie voor de bewerking exporteren/importeren bevatten. De map bash bevat een voorbeeld script dat laat zien hoe u gegevens exporteert van de ZooKeeper-server op het oude cluster en vervolgens weer kunt importeren in de ZooKeeper-server op het nieuwe cluster.

Voer het [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) -script uit van de ZooKeeper-knoop punten om gegevens te exporteren en vervolgens te importeren. Werk het script bij naar de juiste HDP-versie (Hortonworks data platform). (We werken eraan om deze scripts Gene riek in HDInsight te maken. Algemene scripts kunnen vanuit elk knoop punt op het cluster worden uitgevoerd zonder dat de gebruiker hiervoor hoeft te wijzigen.)

De export opdracht schrijft de meta gegevens naar een Apache Hadoop Distributed File System (HDFS) (in Azure Blob Storage of Azure Data Lake Storage) op een locatie die u hebt ingesteld.

### <a name="examples"></a>Voorbeelden

#### <a name="export-offset-metadata"></a>Meta gegevens van verschuiving exporteren

1. Gebruik SSH om naar het ZooKeeper-cluster op het cluster te gaan waarvan de offset van het controle punt moet worden geëxporteerd.
2. Voer de volgende opdracht uit (nadat u de teken reeks voor de HDP-versie hebt bijgewerkt) om ZooKeeper offset-gegevens te exporteren naar het `/stormmetadta/zkdata` HDFS-pad:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Meta gegevens van verschuiving importeren

1. Gebruik SSH om naar het ZooKeeper-cluster op het cluster te gaan waarvan de offset van het controle punt moet worden geïmporteerd.
2. Voer de volgende opdracht uit (nadat u de teken reeks voor de HDP-versie hebt bijgewerkt) om ZooKeeper offset gegevens van het HDFS-pad `/stormmetadata/zkdata` naar de ZooKeeper-server op het doel cluster te importeren:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Verwijder de meta gegevens van verschuiving zodat topologieën kunnen beginnen met het verwerken van gegevens vanaf het begin of van een tijds tempel die de gebruiker kiest

1. Gebruik SSH om naar het ZooKeeper-cluster op het cluster te gaan waarvan de offset van het controle punt moet worden verwijderd.
2. Voer de volgende opdracht uit (nadat u de teken reeks voor de HDP-versie hebt bijgewerkt) om alle ZooKeeper offset-gegevens in het huidige cluster te verwijderen:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hoe kan ik de binaire Storm-bestanden op een cluster vinden?

De binaire Storm-bestanden voor de huidige HDP-stack bevinden zich in `/usr/hdp/current/storm-client` . De locatie is hetzelfde voor hoofd knooppunten en voor worker-knoop punten.

Er zijn mogelijk meerdere binaire bestanden voor specifieke HDP-versies in/usr/HDP (bijvoorbeeld `/usr/hdp/2.5.0.1233/storm` ). De `/usr/hdp/current/storm-client` map is symlinked naar de meest recente versie die op het cluster wordt uitgevoerd.

Zie [verbinding maken met een HDInsight-cluster met behulp van SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) en [Apache Storm](https://storm.apache.org/)voor meer informatie.

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hoe kan ik de implementatie topologie van een storm-cluster bepalen?

Identificeer eerst alle onderdelen die zijn geïnstalleerd met HDInsight Storm. Een storm-cluster bestaat uit vier knooppunt Categorieën:

* Gateway knooppunten
* Hoofd knooppunten
* ZooKeeper-knoop punten
* Worker-knoop punten

### <a name="gateway-nodes"></a>Gateway knooppunten

Een gateway-knoop punt is een gateway en reverse-proxy service die open bare toegang tot een actieve Ambari-beheer service mogelijk maakt. Ook wordt de Ambarie Leader-verkiezing afgehandeld.

### <a name="head-nodes"></a>Hoofd knooppunten

Storm-hoofd knooppunten voeren de volgende services uit:
* Nimbus
* Ambari-server
* Ambari Metrics-server
* Ambari metrische gegevens verzamelaar
 
### <a name="zookeeper-nodes"></a>ZooKeeper-knoop punten

HDInsight wordt geleverd met een ZooKeeper-quorum van drie knoop punten. De quorum grootte is vast en kan niet opnieuw worden geconfigureerd.

Storm Services in het cluster zijn geconfigureerd om automatisch het ZooKeeper-quorum te gebruiken.

### <a name="worker-nodes"></a>Worker-knoop punten

Storm-worker-knoop punten voeren de volgende services uit:
* Supervisor
* Werk nemers van Java virtual machines (JVMs) voor het uitvoeren van topologieën
* Ambari-agent

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hoe kan ik Event Hub binaire bestanden voor het ontwikkelen van Spout niet vinden?

Raadpleeg de volgende bronnen voor meer informatie over het gebruik van Storm Event Hub Spout. jar-bestanden met uw topologie.

### <a name="java-based-topology"></a>Op Java gebaseerde topologie

[Gebeurtenissen uit Azure Event Hubs verwerken met Apache Storm in HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologie op basis van C# (mono op HDInsight 3.4 en Linux Storm-clusters)

[Gebeurtenissen uit Azure Event Hubs verwerken met Apache Storm in HDInsight (C#)](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Nieuwste Apache Storm Event Hub Spout binaire bestanden voor HDInsight 3.5-en Linux Storm-clusters

Zie het [Leesmij-bestand voor MVN-opslag plaats](https://github.com/hdinsight/mvn-repo/blob/master/README.md)voor meer informatie over het gebruik van de nieuwste Storm Event hub Spout die werkt met HDInsight 3.5-en Linux Storm-clusters.

### <a name="source-code-examples"></a>Voor beelden van bron code

Zie [voor beelden](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) van het lezen en schrijven van Azure Event hub met behulp van een Apache Storm topologie (geschreven in Java) op een Azure HDInsight-cluster.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Hoe kan ik wilt u Storm Log4J 2 configuratie bestanden in clusters vinden?

[Apache Log4j 2](https://logging.apache.org/log4j/2.x/) -configuratie bestanden voor Storm-Services identificeren.

### <a name="on-head-nodes"></a>Op hoofd knooppunten

De configuratie van de Nimbus Log4J wordt gelezen `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

### <a name="on-worker-nodes"></a>Op worker-knoop punten

De configuratie van de Super Visor Log4J wordt gelezen `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

Het configuratie bestand van de worker-Log4J wordt gelezen `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` .

Vindt `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Geen uitzonde ring voor Leader

Bij het verzenden van een topologie kan een fout bericht van de gebruiker worden weer gegeven die er ongeveer als volgt uitziet: `Topology submission exception, cause not a leader, the current leader is NimbusInfo` .

Om dit probleem op te lossen moet de gebruiker mogelijk een ticket indienen om de knoop punten opnieuw te starten of opnieuw op te starten. Zie [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html) voor meer informatie.

---

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).