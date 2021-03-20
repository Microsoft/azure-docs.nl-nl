---
title: Azure HDInsight 3,6 Apache Storm migreren naar HDInsight 4,0 Apache Spark
description: De verschillen en migratie stroom voor het migreren van Apache Storm workloads naar Spark streaming of Spark Structured streaming.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: aa57c01558cfdcf069b17fad9e86f7640553dcfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944778"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Azure HDInsight 3,6 Apache Storm migreren naar HDInsight 4,0 Apache Spark

In dit document wordt beschreven hoe Apache Storm workloads op HDInsight 3,6 naar HDInsight 4,0 worden gemigreerd. HDInsight 4,0 biedt geen ondersteuning voor het cluster type Apache Storm en u moet migreren naar een ander streaming-gegevens platform. Er zijn twee geschikte opties Apache Spark streaming en Spark Structured streaming. In dit document worden de verschillen tussen deze platformen beschreven en wordt ook een werk stroom aanbevolen voor het migreren van Apache Storm workloads.

## <a name="storm-migration-paths-in-hdinsight"></a>Storm-migratie paden in HDInsight

Als u wilt migreren vanaf Apache Storm op HDInsight 3,6, hebt u meerdere opties:

* Spark-streaming op HDInsight 4,0
* Spark Structured streaming op HDInsight 4,0
* Azure Stream Analytics

Dit document bevat een hand leiding voor het migreren van Apache Storm naar Spark streaming en Spark Structured streaming.

> [!div class="mx-imgBorder"]
> ![Migratie pad voor HDInsight Storm](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Vergelijking tussen Apache Storm en Spark-streaming, met Spark gestructureerde streaming

Apache Storm kan verschillende niveaus van gegarandeerde berichtverwerking bieden. Een eenvoudige Storm-toepassing kan bijvoorbeeld een verwerkings snelheid van ten minste één keer garanderen en [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) kan gegarandeerd precies één keer worden verwerkt. Spark streaming en Spark Structured streaming garanderen dat elke invoer gebeurtenis precies één keer wordt verwerkt, zelfs als er een storing in een knoop punt optreedt. Storm heeft een model dat elke gebeurtenis verwerkt, en u kunt ook het micro batch-model gebruiken met Trident. Spark streaming en Spark Structured streaming bieden Micro-Batch verwerkings model.

|  |Storm |Spark-streaming | Spark Structured streaming|
|---|---|---|---|
|**Garantie voor gebeurtenis verwerking**|Ten minste één keer <br> Slechts eenmaal (Trident) |[Slechts eenmaal](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Slechts eenmaal](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Verwerkings model**|Real-time <br> Micro batch (Trident) |Micro batch |Micro batch |
|**Ondersteuning voor gebeurtenis tijden**|[Ja](https://storm.apache.org/releases/2.0.0/Windowing.html)|Nee|[Ja](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Talen**|Java, etc.|Scala, Java, python|Python, R, scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark-streaming versus gestructureerde streaming in Spark

Spark Structured streaming vervangt Spark-streaming (DStreams). Structured streaming blijft uitbrei dingen en onderhoud ontvangen, terwijl DStreams alleen in de onderhouds modus wordt uitgevoerd. **Opmerking: er zijn koppelingen nodig om dit punt te benadrukken**. Structured streaming heeft niet zo veel functies als DStreams voor de bronnen en sinks die deze niet ondersteunt. Controleer daarom uw vereisten om de juiste optie voor de verwerking van Spark-stream te kiezen.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Streaming-verwerking (één gebeurtenis) versus Micro-Batch verwerking

Storm biedt een model waarmee elke gebeurtenis wordt verwerkt. Dit betekent dat alle inkomende records worden verwerkt zodra ze binnenkomen. Toepassingen met Spark-streaming moeten een fractie van een seconde wachten op het verzamelen van elke micro batch gebeurtenissen voordat deze batch wordt verzonden voor verwerking. Daarentegen wordt elke gebeurtenis direct verwerkt door een gebeurtenis gerichte toepassing. De latentie van Spark-streaming is doorgaans een paar seconden. De voor delen van de micro batch-benadering zijn een efficiëntere verwerking van gegevens en complexe berekeningen.

> [!div class="mx-imgBorder"]
> ![streaming en micro batch-verwerking](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Storm-architectuur en-onderdelen

Storm-topologieën bestaan uit meerdere onderdelen die zijn gerangschikt in een Directed Acyclic Graph (DAG). Gegevens stromen tussen de onderdelen in de grafiek. Elk onderdeel verbruikt een of meer gegevensstromen en kan eventueel een of meer stromen genereren.

|Onderdeel |Beschrijving |
|---|---|
|Spout|Haalt gegevens op in een topologie. Deze onderdelen introduceren een of meer stromen in de topologie.|
|Montagekit|Verbruikt stromen die zijn verzonden vanuit spouts of andere schichten. Bolts kunnen eventueel nieuwe stromen in de topologie introduceren. Bolts zijn ook verantwoordelijk voor het wegschrijven van gegevens naar externe services of opslag, zoals HDFS, Kafka of HBase.|

> [!div class="mx-imgBorder"]
> ![interactie van Storm-onderdelen](./media/migrate-storm-to-spark/apache-storm-components.png)

Storm bestaat uit de volgende drie daemons, waarmee het Storm-cluster goed werkt.

|Daemon |Beschrijving |
|---|---|
|Nimbus|Net als bij Hadoop JobTracker is het verantwoordelijk voor het distribueren van code rond het cluster en het toewijzen van taken aan machines en het bewaken van fouten.|
|Zookeeper|Wordt gebruikt voor de coördinatie van het cluster.|
|Supervisor|Luistert naar werk dat is toegewezen aan de computer en start en stopt werk processen op basis van de instructies van Nimbus. Elk werk proces voert een subset van een topologie uit. De toepassings logica van de gebruiker (Spouts en bout) voert u hier in.|

> [!div class="mx-imgBorder"]
> ![Nimbus-, Zookeeper-en supervisor-daemons](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Infra structuur en onderdelen van Spark-streaming

In de volgende stappen wordt beschreven hoe onderdelen in Spark streaming (DStreams) en Spark Structured streaming worden gebruikt:

* Wanneer Spark streaming wordt gestart, start het stuur programma de taak in de uitvoerder.
* De uitvoerder ontvangt een stroom van een streaming-gegevens bron.
* Wanneer de uitvoerder gegevens stromen ontvangt, wordt de stroom in blokken gesplitst en blijven ze in het geheugen.
* Gegevens blokken worden gerepliceerd naar andere uitvoerendeers.
* De verwerkte gegevens worden vervolgens opgeslagen in het doel gegevens archief.

> [!div class="mx-imgBorder"]
> ![Spark streaming-pad naar uitvoer](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>DStream-werk stroom (Spark streaming)

Als elke batch-interval is verstreken, wordt er een nieuw RDD gemaakt dat alle gegevens uit dat interval bevat. De doorlopende sets van Rdd's worden verzameld in een DStream. Als het batch-interval bijvoorbeeld één seconde lang is, verzendt uw DStream elke seconde een batch met een RDD die alle gegevens bevat die tijdens die seconde zijn opgenomen. Bij het verwerken van de DStream wordt de temperatuur gebeurtenis weer gegeven in een van deze batches. Een Spark-streaming-toepassing verwerkt de batches die de gebeurtenissen bevatten en werkt uiteindelijk op de gegevens die zijn opgeslagen in elke RDD.

> [!div class="mx-imgBorder"]
> ![verwerkings batches voor Spark-streaming](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Zie trans [formaties op DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)voor meer informatie over de verschillende trans formaties die beschikbaar zijn met Spark streaming.

## <a name="spark-structured-streaming"></a>Spark Structured streaming

Met Spark Structured streaming wordt een gegevens stroom aangeduid als een tabel waarvan de diepte niet is beperkt. De tabel blijft groeien naarmate er nieuwe gegevens binnenkomen. Deze invoer tabel wordt continu verwerkt door een langlopende query en de resultaten worden naar een uitvoer tabel verzonden.

In structured streaming worden gegevens in het systeem bezorgd en onmiddellijk opgenomen in een invoer tabel. U schrijft query's (met behulp van de data frame-en DataSet-Api's) die bewerkingen uitvoeren op deze invoer tabel.

De query-uitvoer levert een *resultaat tabel* op die de resultaten van de query bevat. U kunt gegevens uit de resultaten tabel voor een extern gegevens archief, zoals een relationele data base, tekenen.

Het tijdstip waarop de gegevens worden verwerkt vanuit de invoer tabel, wordt bepaald door het trigger interval. Standaard is het trigger interval nul, zodat gestructureerde streaming probeert de gegevens te verwerken zodra deze binnenkomen. In de praktijk betekent dit dat zodra Structured streaming de uitvoering van de vorige query heeft verwerkt, een andere verwerkings uitvoering wordt gestart op basis van recent ontvangen gegevens. U kunt de trigger zo configureren dat deze wordt uitgevoerd met een interval, zodat de streaminggegevens worden verwerkt in op tijd gebaseerde batches.

> [!div class="mx-imgBorder"]
> ![verwerking van gegevens in gestructureerde streaming](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![programmeer model voor Structured streaming](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Algemene migratie stroom

De aanbevolen migratie stroom van Storm naar Spark gaat ervan uit dat de volgende initiële architectuur:

* Kafka wordt gebruikt als streaming-gegevens bron
* Kafka en Storm worden geïmplementeerd op hetzelfde virtuele netwerk
* De gegevens die worden verwerkt door storm, worden geschreven naar een gegevens sink, zoals Azure Storage of Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagram van de veronderstelde huidige omgeving](./media/migrate-storm-to-spark/presumed-current-environment.png)

Ga als volgt te werk om uw toepassing te migreren van Storm naar een van de Spark-streaming Api's:

1. **Implementeer een nieuw cluster.** Implementeer een nieuw HDInsight 4,0 Spark-cluster in hetzelfde virtuele netwerk en implementeer uw toepassing met Spark-streaming of Spark Structured streaming en test het zorgvuldig.

    > [!div class="mx-imgBorder"]
    > ![nieuwe Spark-implementatie in HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Stop het verbruik van het oude Storm-cluster.** Stop met het gebruiken van gegevens uit de gegevens bron streamen in de bestaande Storm en wacht totdat de gegevens zijn geschreven naar de doel-sink.

    > [!div class="mx-imgBorder"]
    > ![stoppen met verbruik op het huidige cluster](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Begin met verbruik van het nieuwe Spark-cluster.** Streamen van gegevens vanaf een nieuw geïmplementeerd HDInsight 4,0 Spark-cluster starten. Op dit moment wordt het proces overgenomen door gebruik te gaan van de meest recente Kafka-offset.

    > [!div class="mx-imgBorder"]
    > ![beginnen met verbruik op nieuw cluster](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Verwijder het oude cluster als dat nodig is.** Zodra de switch is voltooid en correct werkt, verwijdert u het oude 3,6 HDInsight-cluster van de verouderde, indien nodig.

    > [!div class="mx-imgBorder"]
    > ![oude HDInsight-clusters indien nodig verwijderen](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer informatie over Storm, Spark streaming en Spark Structured streaming:

* [Overzicht van Apache Spark-streaming](../spark/apache-spark-streaming-overview.md)
* [Overzicht van Gestructureerde streaming van Apache Spark](../spark/apache-spark-structured-streaming-overview.md)
