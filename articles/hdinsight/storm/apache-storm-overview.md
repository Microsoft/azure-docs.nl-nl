---
title: Wat is Apache Storm - Azure HDInsight
description: Met Apache Storm kunt u gegevensstromen in realtime verwerken. Met Azure HDInsight kunt u eenvoudig Storm-clusters maken in de Azure-cloud. Met Visual Studio kunt u Storm-oplossingen schrijven in C# en deze vervolgens implementeren in uw HDInsight Storm-clusters.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: a2bca34f683df27c97b131d57c6c3278f7cb175e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928904"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Wat is Apache Storm in Azure HDInsight?

[Apache Storm](https://storm.apache.org/) is een gedistribueerd, fouttolerant en open-source computingsysteem. U kunt Storm gebruiken om gegevensstromen in realtime te verwerken met [Apache Hadoop](../hadoop/apache-hadoop-introduction.md). Storm-oplossingen kunnen ook een gegarandeerde verwerking van gegevens bieden, met de mogelijkheid om gegevens die de eerste keer niet zijn verwerkt, opnieuw af te spelen.

## <a name="why-use-apache-storm-on-hdinsight"></a>Waarom zou u Apache Storm in HDInsight gebruiken?

Storm op HDInsight biedt de volgende functies:

* __99% SLA (Service Level Agreement) voor Storm-uptime__: Storm in HDInsight wordt geleverd met volledige en onafgebroken ondersteuning. Storm op HDInsight beschikt eveneens over een SLA van 99,9 procent. Dit betekent dat Microsoft garandeert dat een Storm-cluster minimaal 99,9 procent van de tijd externe verbinding heeft. Zie [Ondersteuning van Azure](https://azure.microsoft.com/support/options/) voor meer informatie. Zie ook het document met [SLA-informatie voor HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Ondersteunt de mogelijkheid om eenvoudig aanpassingen te implementeren door tijdens of na het maken van een Storm-cluster scripts voor dat cluster uit te voeren. Zie [HDInsight-clusters aanpassen met scriptacties](../hdinsight-hadoop-customize-cluster-linux.md) voor meer informatie.

* **Oplossingen maken in meerdere talen**: u kunt Storm-onderdelen schrijven in de taal van uw keuze, bijvoorbeeld in Java, C# of Python.

    * Integreert Visual Studio met HDInsight voor het ontwikkelen, beheren en controleren van C#-topologieën. Zie [C# Storm-topologieën met de hulpprogramma's van HDInsight voor Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) voor meer informatie.

    * Biedt ondersteuning voor de Trident Java-interface. U kunt Storm-topologieën maken die ondersteuning bieden voor een eenmalige verwerking van berichten, transactionele DataStore-persistentie en een aantal algemene Stream Analytics-bewerkingen.

* **Dynamische schaalbaarheid**: u kunt werkknooppunten toevoegen of verwijderen zonder de actieve Storm-topologieën te beïnvloeden. Deactiveer en reactiveer de lopende topologieën om te profiteren van de nieuwe knooppunten die zijn toegevoegd via vergroten/verkleinen.

* **Streaming-pijplijn maken met meerdere Azure-Services**: Storm in HDInsight kan worden geïntegreerd met andere Azure-Services. Denk bijvoorbeeld aan Event Hubs, SQL Database, Azure Storage en Azure Data Lake Storage. Zie voor een voorbeeldoplossing die kan worden geïntegreerd met Azure-services [Gebeurtenissen verwerken van Event Hubs met Apache Storm in HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

Zie [deze Engelstalige site](https://storm.apache.org/Powered-By.html) voor een lijst met bedrijven die Apache Storm gebruiken voor hun oplossingen voor realtime analyse.

Raadpleeg om aan de slag te gaan met Storm [Apache Storm-topologie maken/bewaken in Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Hoe werkt Apache Storm?

Storm voert topologieën uit in plaats van de [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)-taken die u mogelijk kent. Storm-topologieën bestaan uit meerdere onderdelen die zijn gerangschikt in een Directed Acyclic Graph (DAG). Gegevens stromen tussen de onderdelen in de grafiek. Elk onderdeel verbruikt een of meer gegevensstromen en kan eventueel een of meer stromen genereren. Het volgende diagram toont hoe de gegevens stromen tussen de onderdelen van een eenvoudige topologie voor het tellen van woorden:

![Voorbeeld van hoe onderdelen zijn gerangschikt in een Storm-topologie](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Via Spout-onderdelen worden gegevens overgebracht naar een topologie. Deze onderdelen introduceren een of meer stromen in de topologie.

* Bolt-onderdelen verwerken stromen die afkomstig zijn van spouts of andere bolts. Bolts kunnen eventueel nieuwe stromen in de topologie introduceren. Bolts zijn ook verantwoordelijk voor het wegschrijven van gegevens naar externe services of opslag, zoals HDFS, Kafka of HBase.

## <a name="reliability"></a>Betrouwbaarheid

Apache Storm zorgt ervoor dat elk binnenkomend bericht altijd volledig wordt verwerkt, zelfs wanneer de gegevensanalyse is verspreid over honderden knooppunten.

Het Nimbus-knooppunt biedt vergelijkbare functionaliteit als de Apache Hadoop JobTracker. Nimbus wijst taken toe aan andere knooppunten in een cluster via Apache ZooKeeper. Zookeeper-knooppunten geven een cluster coördinatiemogelijkheden en faciliteren de communicatie tussen Nimbus en het Supervisor-proces op de werkknooppunten. Als een verwerkingsknooppunt wordt uitgeschakeld, wordt het Nimbus-knooppunt hiervan op de hoogte gesteld en worden de taak en de bijbehorende gegevens toegewezen aan een ander knooppunt.

Apache Storm-clusters worden standaard geconfigureerd met slechts één Nimbus-knooppunt. Storm in HDInsight ondersteunt twee Nimbus-knooppunten. Als het primaire knooppunt uitvalt, schakelt het Storm-cluster over naar het secundaire knooppunt en wordt het primaire knooppunt hersteld. Het volgende diagram illustreert de taakstroomconfiguratie voor Storm op HDInsight:

![Diagram van nimbus, zookeeper en supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-use"></a>Gebruiksgemak

|Gebruik |Beschrijving |
|---|---|
|SSH-connectiviteit (Secure Shell)|U hebt via internet toegang tot de hoofdknooppunten van het Storm-cluster met behulp van SSH. U kunt opdrachten rechtstreeks op het cluster uitvoeren met behulp van SSH. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.|
|Webconnectiviteit|Alle HDInsight-clusters bieden de Ambari-webgebruikersinterface. Met de Ambare-webgebruikersinterface kunt u eenvoudig services op het cluster controleren, configureren en beheren. Storm-clusters bieden ook de Storm-gebruikersinterface. Met behulp van de Storm-gebruikersinterface kunt u actieve Storm-topologieën controleren en beheren vanuit de browser. Zie de documenten [HDInsight beheren met behulp van de Apache Ambari-webgebruikersinterface](../hdinsight-hadoop-manage-ambari.md) en [Controleren en beheren met behulp van de Apache Storm-gebruikersinterface](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) voor meer informatie.|
|Azure PowerShell en Azure CLI|PowerShell en Azure CLI bevatten allebei opdrachtregelprogramma's die u vanuit het clientsysteem kunt gebruiken om te werken met HDInsight en andere Azure-services.|
|Integratie met Visual Studio|Azure Data Lake-tools voor Visual Studio bevatten projectsjablonen voor het maken van C# Storm-topologieën met behulp van het SCP.NET-framework. Data Lake Tools biedt ook hulpprogramma's voor het implementeren, bewaken en beheren van oplossingen met Storm op HDInsight. Zie [C# Storm-topologieën met de hulpprogramma's van HDInsight voor Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) voor meer informatie.|

## <a name="integration-with-other-azure-services"></a>Integratie met andere Azure-services

* __Azure Data Lake Storage__: Zie [Azure Data Lake Storage gebruiken met Apache Storm in HDInsight](apache-storm-write-data-lake-store.md).

* __Azure Event Hubs__: raadpleeg de volgende voorbeelden voor een voorbeeld van het gebruik van Event Hubs met een Storm-cluster:

    * [Gebeurtenissen uit Azure Event Hubs verwerken met Apache Storm in HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Gebeurtenissen uit Azure Event Hubs verwerken met Apache Storm in HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL-Database__, __Cosmos DB__, __Event Hubs__ en __HBase__: in de Data Lake-hulpmiddelen voor Visual Studio zijn sjabloonvoorbeelden opgenomen. Zie [Een C#-topologie voor Apache Storm op HDInsight ontwikkelen](apache-storm-develop-csharp-visual-studio-topology.md) voor meer informatie.

## <a name="apache-storm-use-cases"></a>Use cases van Apache Storm

Hier volgen enkele veelvoorkomende scenario's waarvoor u Storm in HDInsight zou kunnen gebruiken:

* Internet der dingen (IoT)
* Fraudedetectie
* Sociale analyses
* Extractie, transformatie en laden (ETL)
* Netwerkbewaking
* Search
* Mobile Engagement

Lees het document [Hoe bedrijven Apache Storm gebruiken](https://storm.apache.org/Powered-By.html) voor informatie over praktijkscenario's.

## <a name="development"></a>Ontwikkeling

Met hulp van Data Lake-tools voor Visual Studio kunnen .NET-ontwikkelaars topologieën ontwerpen en implementeren voor Visual Studio. U kunt ook hybride topologieën maken die gebruikmaken van Java- en C#-onderdelen. Zie [C#-topologieën met Visual Studio ontwikkelen voor Apache Storm op HDInsight](apache-storm-develop-csharp-visual-studio-topology.md) voor meer informatie.

U kunt ook Java-oplossingen ontwikkelen met behulp van de IDE van uw keuze. Zie [Java-topologieën ontwikkelen voor Apache Storm in HDInsight](apache-storm-develop-java-topology.md) voor meer informatie.

Python kan ook worden gebruikt voor het ontwikkelen van Storm-onderdelen. Zie [Storm-topologieën ontwikkelen met Python voor Apache Storm in HDInsight](apache-storm-develop-python-topology.md) voor meer informatie.

## <a name="common-development-patterns"></a>Algemene ontwikkelingspatronen

### <a name="guaranteed-message-processing"></a>Gegarandeerde berichtverwerking

Apache Storm kan verschillende niveaus van gegarandeerde berichtverwerking bieden. Een eenvoudige Storm-toepassing biedt u bijvoorbeeld de garantie dat de gegevens minimaal één keer worden verwerkt. Trident kan u de garantie bieden dat gegevens exact één keer worden verwerkt. Zie [Guarantees on data processing](https://storm.apache.org/about/guarantees-data-processing.html) (Garanties met betrekking tot de gegevensverwerking) op apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Het patroon voor het lezen van een invoer-tuple, het verzenden van nul of meer tuples om de invoer-tuple vervolgens onmiddellijk aan het einde van de uitvoeringsmethode te bevestigen, is een veelgebruikte methode. Storm biedt de [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html)-interface voor het automatiseren van dit patroon.

### <a name="joins"></a>Samenvoegingen

Hoe gegevensstromen worden gekoppeld, varieert per toepassing. U kunt bijvoorbeeld elke tuple uit meerdere streams samenvoegen in één nieuwe stream, of u kunt alleen batches tuples voor een specifiek venster samenvoegen. In beide gevallen kunt u hiervoor [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) gebruiken. Veldgroepering is een manier om te definiëren hoe tuples worden gerouteerd naar bolts.

In het volgende Java-voorbeeld wordt fieldsGrouping gebruikt om tuples die afkomstig zijn uit de onderdelen 1, 2 en 3, te routeren naar de MyJoiner-bolt:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Batches

Apache Storm biedt een intern timingmechanisme dat bekend staat als een 'tick tuple'. U kunt instellen hoe vaak in uw topologie een tick tuple wordt verzonden.

Zie [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java) voor een voorbeeld van het gebruik van een tick tuple uit een C#-onderdeel.

### <a name="caches"></a>Caches

In-memory caching wordt vaak gebruikt als mechanisme om de verwerking te versnellen, omdat de veelgebruikte assets in het geheugen blijven staan. Omdat een topologie wordt verdeeld over meerdere knooppunten en meerdere processen binnen elk knooppunt, is het goed om het gebruik van [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) te overwegen. Gebruik `fieldsGrouping` om ervoor te zorgen dat tuples met velden die worden gebruikt voor het opzoeken van de cache, altijd naar hetzelfde proces worden gerouteerd. Met deze groeperingsfunctionaliteit voorkomt u dubbele cachevermeldingen voor verschillende processen.

### <a name="stream-top-n"></a>'Top N' van stromen

Wanneer uw topologie afhankelijk is van de berekening van de waarde van een 'top N', moet u die waarde in parallel berekenen. Vervolgens moet u de uitvoer van die berekeningen samenvoegen tot een globale waarde. Dit kan door met [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) op veld te routeren voor parallelle verwerking. Vervolgens kunt u de uitvoer doorsturen naar een bolt die globaal de top N-waarde bepaalt.

Zie het [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java)-voorbeeld voor een voorbeeld van een berekening van een top N-waarde.

## <a name="logging"></a>Logboekregistratie

Storm gebruikt Apache Log4j 2 om informatie in een logboek te registreren. Standaard wordt een grote hoeveelheid gegevens geregistreerd en kan het lastig zijn om de informatie te doorzoeken. U kunt een configuratiebestand voor logboekregistratie opnemen als onderdeel van uw Storm-topologie om de werking van de logboekregistratie te bepalen.

Zie het voorbeeld van een [op Java gebaseerde woordentelling](apache-storm-develop-java-topology.md) voor Storm op HDInsight, voor een voorbeeldtopologie die aantoont hoe u de logboekregistratie moet configureren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over realtime analyseoplossingen met Apache Storm in HDInsight:

* [Apache Storm-topologie maken/bewaken in Azure HDInsight](apache-storm-quickstart.md)
* [Example Storm toplogies and components for Apache Storm on HDInsight](apache-storm-example-topology.md) (Voorbeelden van Storm-topologieën en -onderdelen in HDInsight)
