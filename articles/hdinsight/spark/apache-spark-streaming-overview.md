---
title: Spark-streaming in azure HDInsight
description: Apache Spark streaming-toepassingen gebruiken in HDInsight Spark-clusters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: bde6c5b2bad12df8642dd3c9b4a49548f7bc9a6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929522"
---
# <a name="overview-of-apache-spark-streaming"></a>Overzicht van Apache Spark-streaming

[Apache Spark](https://spark.apache.org/) Streaming biedt verwerking van gegevens stromen in HDInsight Spark-clusters. Met een garantie dat elke invoer gebeurtenis precies één keer wordt verwerkt, zelfs als er een storing in een knoop punt optreedt. Een Spark-stroom is een langlopende taak waarmee invoer gegevens worden ontvangen van een groot aantal verschillende bronnen, waaronder Azure Event Hubs. Ook: Azure IoT Hub, Apache Kafka, Apache Flume, Twitter, `ZeroMQ` , onbewerkte TCP-sockets of bewaken Apache HADOOP garen bestands systeem. In tegens telling tot een enkel gebeurtenis gerichte proces, voert een Spark-stream batches invoer gegevens uit in tijd Vensters. Zoals een segment van twee seconden, en transformeert elke batch met gegevens met behulp van de bewerkingen kaart, verminderen, samen voegen en extra heren. De Spark-stroom schrijft vervolgens de getransformeerde gegevens uit naar bestands systeem, data bases, Dash boards en de-console.

![Stroom verwerking met HDInsight en Spark-streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark-streaming-toepassingen moeten een fractie van een seconde wachten op het verzamelen `micro-batch` van alle gebeurtenissen voordat deze batch wordt verzonden voor verwerking. Daarentegen wordt elke gebeurtenis direct verwerkt door een gebeurtenis gerichte toepassing. De latentie van Spark-streaming is doorgaans een paar seconden. De voor delen van de micro batch-benadering zijn een efficiëntere verwerking van gegevens en complexe berekeningen.

## <a name="introducing-the-dstream"></a>Introductie van de DStream

Spark streaming vertegenwoordigt een doorlopende stroom van inkomende gegevens met behulp van een *onderscheiden-stroom* die een DStream wordt genoemd. Een DStream kan worden gemaakt op basis van invoer bronnen zoals Event Hubs of Kafka. Of door trans formaties toe te passen op een andere DStream.

Een DStream biedt een laag van abstractie boven op de onbewerkte gebeurtenis gegevens.

Begin met één gebeurtenis, zeg een Tempe ratuur bij het lezen van een verbonden Thermo staat. Wanneer deze gebeurtenis arriveert bij uw Spark-streaming-toepassing, wordt de gebeurtenis op een betrouw bare manier opgeslagen, waar deze wordt gerepliceerd op meerdere knoop punten. Deze fout tolerantie zorgt ervoor dat de storing van een enkel knoop punt niet leidt tot verlies van uw gebeurtenis. De Spark-kern maakt gebruik van een gegevens structuur waarmee gegevens over meerdere knoop punten in het cluster worden gedistribueerd. Waarbij elk knoop punt in het algemeen eigen gegevens in het geheugen onderhoudt voor de beste prestaties. Deze gegevens structuur wordt een *flexibele gedistribueerde gegevensset* (RDD) genoemd.

Elke RDD vertegenwoordigt gebeurtenissen die worden verzameld over een door de gebruiker gedefinieerde periode met de naam *batch-interval*. Als elke batch-interval is verstreken, wordt er een nieuw RDD gemaakt dat alle gegevens uit dat interval bevat. De doorlopende set Rdd's wordt verzameld in een DStream. Als het batch-interval bijvoorbeeld één seconde lang is, verzendt uw DStream elke seconde een batch met een RDD die alle gegevens bevat die tijdens die seconde zijn opgenomen. Bij het verwerken van de DStream wordt de temperatuur gebeurtenis weer gegeven in een van deze batches. Een Spark-streaming-toepassing verwerkt de batches die de gebeurtenissen bevatten en werkt uiteindelijk op de gegevens die zijn opgeslagen in elke RDD.

![Voor beeld van DStream met een temperatuur gebeurtenis](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Structuur van een Spark-streaming-toepassing

Een Spark streaming-toepassing is een langlopende toepassing die gegevens van opname bronnen ontvangt. Hiermee worden trans formaties toegepast om de gegevens te verwerken en vervolgens worden de gegevens naar een of meer bestemmingen gepusht. De structuur van een Spark-streaming-toepassing heeft een statisch onderdeel en een dynamisch deel. Met het statische deel wordt gedefinieerd waar de gegevens vandaan komen, welke bewerking moet worden uitgevoerd op de gegevens. En waar de resultaten moeten worden geplaatst. Het dynamische deel voert de toepassing voor onbepaalde tijd uit, waarbij wordt gewacht op een stop signaal.

De volgende eenvoudige toepassing ontvangt bijvoorbeeld een tekst regel over een TCP-socket en telt het aantal keer dat elk woord wordt weer gegeven.

### <a name="define-the-application"></a>De toepassing definiëren

De definitie van de toepassings logica bestaat uit vier stappen:

1. Een StreamingContext maken.
2. Maak een DStream van de StreamingContext.
3. Trans formaties Toep assen op de DStream.
4. De resultaten worden uitgevoerd.

Deze definitie is statisch en er worden geen gegevens verwerkt totdat u de toepassing uitvoert.

#### <a name="create-a-streamingcontext"></a>Een StreamingContext maken

Maak een StreamingContext van de SparkContext die naar uw cluster verwijst. Wanneer u een StreamingContext maakt, geeft u in seconden de grootte van de batch op, bijvoorbeeld:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Een DStream maken

Maak met het StreamingContext-exemplaar een invoer-DStream voor de invoer bron. In dit geval kijkt de toepassing naar de weer gave van nieuwe bestanden in de standaard Attached Storage.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Transformaties toepassen

U implementeert de verwerking door trans formaties op de DStream toe te passen. Deze toepassing ontvangt één regel tekst per keer uit het bestand, splitst elke regel in woorden. En maakt vervolgens gebruik van een patroon voor de toewijzing en het aantal keren dat elk woord wordt weer gegeven.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Uitvoer resultaten

Het pushen van de trans formatie resulteert naar de doel systemen door uitvoer bewerkingen toe te passen. In dit geval wordt het resultaat van elke uitvoering via de berekening afgedrukt in de console-uitvoer.

```
wordCounts.print()
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Start de streaming-toepassing en voer uit totdat een beëindigings signaal wordt ontvangen.

```
ssc.start()
ssc.awaitTermination()
```

Zie [Apache Spark streaming-programmeer gids](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)voor meer informatie over de Spark stream API.

De volgende voorbeeld toepassing is op zichzelf opgenomen, zodat u deze kunt uitvoeren in een [Jupyter notebook](apache-spark-jupyter-notebook-kernels.md). In dit voor beeld wordt een model gegevens bron gemaakt in de klasse DummySource die de waarde van een teller en de huidige tijd in milliseconden om de vijf seconden uitvoert. Een nieuw StreamingContext-object heeft een batch-interval van 30 seconden. Telkens wanneer er een batch wordt gemaakt, onderzoekt de streaming-toepassing de RDD die zijn geproduceerd. Converteert de RDD vervolgens naar een Spark data frame en maakt een tijdelijke tabel over de data frame.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Wacht ongeveer 30 seconden nadat de bovenstaande toepassing is gestart.  Vervolgens kunt u de data frame regel matig opvragen om de huidige set met waarden te zien die aanwezig zijn in de batch, bijvoorbeeld met behulp van deze SQL-query:

```sql
%%sql
SELECT * FROM demo_numbers
```

De resulterende uitvoer ziet er als volgt uit:

| waarde | tijd |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Er zijn zes waarden, omdat de DummySource elke vijf seconden een waarde maakt en de toepassing elke 30 seconden een batch genereert.

## <a name="sliding-windows"></a>Vensters verschuiven

Als u statistische berekeningen wilt uitvoeren op uw DStream over een bepaalde periode, bijvoorbeeld om de gemiddelde Tempe ratuur voor de laatste twee seconden te krijgen, gebruikt u de `sliding window` bewerkingen die zijn opgenomen in Spark streaming. Een sliding window heeft een duur (de lengte van het venster) en het interval waarmee de inhoud van het venster wordt geëvalueerd (het interval van de dia).

Schuif vensters kunnen elkaar overlappen, maar u kunt bijvoorbeeld een venster met een lengte van twee seconden definiëren dat elke seconde een dia bevat. Deze actie betekent elke keer dat u een aggregatie berekening uitvoert, het venster bevat gegevens van de laatste één seconde van het vorige venster. En nieuwe gegevens in de volgende seconde.

![Voor beeld van een begin venster met temperatuur gebeurtenissen](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Voorbeeld venster met temperatuur gebeurtenissen na het schuiven](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

In het volgende voor beeld wordt de code die gebruikmaakt van de DummySource, gebruikt om de batches te verzamelen in een venster met een duur van één minuut en een dia van één minuut.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Na de eerste minuut zijn er 12 vermeldingen-zes items van elk van de twee in het venster verzamelde batches.

| waarde | tijd |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

De sliding window functies die beschikbaar zijn in de Spark streaming API omvatten Window, countByWindow, reduceByWindow en countByValueAndWindow. Zie trans [formaties op DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams)voor meer informatie over deze functies.

## <a name="checkpointing"></a>Controlepunten maken

Voor het leveren van tolerantie en fout tolerantie is de controle punten voor Spark-streaming vereist om ervoor te zorgen dat de stroom verwerking niet kan worden onderbroken, zelfs in het geval van storingen in knoop punten. Spark maakt controle punten voor duurzame opslag (Azure Storage of Data Lake Storage). Deze controle punten slaan meta gegevens van de toepassing op, zoals de configuratie, en de bewerkingen die door de toepassing zijn gedefinieerd. Ook alle batches die in de wachtrij zijn geplaatst, maar nog niet zijn verwerkt. Soms bevatten de controle punten ook de gegevens in de Rdd's om de status van de gegevens snel opnieuw op te bouwen van wat er aanwezig is in de Rdd's die wordt beheerd door Spark.

## <a name="deploying-spark-streaming-applications"></a>Toepassingen voor Spark-Streaming implementeren

Doorgaans bouwt u een Spark-streaming-toepassing lokaal op in een JAR-bestand. Implementeer deze vervolgens in Spark op HDInsight door het JAR-bestand te kopiëren naar de standaard Attached Storage. U kunt de toepassing starten met de LIVY REST-Api's die beschikbaar zijn vanaf uw cluster met behulp van een POST-bewerking. De hoofd tekst van het bericht bevat een JSON-document dat het pad naar uw JAR verschaft. En de naam van de klasse waarvan de methode main de streaming-toepassing definieert en uitvoert, en optioneel de resource vereisten van de taak (zoals het aantal uitvoerendeers, het geheugen en de kernen). Daarnaast zijn er configuratie-instellingen vereist voor de toepassings code.

![Een Spark streaming-toepassing implementeren](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

De status van alle toepassingen kan ook worden gecontroleerd met een GET-aanvraag voor een LIVY-eind punt. Ten slotte kunt u een actieve toepassing beëindigen door een DELETE-aanvraag uit te geven voor het LIVY-eind punt. Zie [externe taken met Apache LIVY](apache-spark-livy-rest-interface.md) voor meer informatie over de LIVY-API

## <a name="next-steps"></a>Volgende stappen

* [Een Apache Spark-cluster maken in Azure HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmeer gids voor Apache Spark streaming](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Overzicht van Gestructureerde streaming van Apache Spark](apache-spark-structured-streaming-overview.md)
