---
title: Maxi maal beschik bare Spark streaming-taken in GARENs-Azure HDInsight
description: Apache Spark streaming instellen voor een scenario met een hoge Beschik baarheid in azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 602fa1cab71a797dd25aca263e0c6a9f2aa616bb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870225"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Apache Spark streaming-taken met hoge Beschik baarheid maken met GARENs

[Apache Spark](https://spark.apache.org/) Met streaming kunt u schaal bare en fout tolerante toepassingen met een hoge door Voer implementeren voor de verwerking van gegevens stromen. U kunt Spark streaming-toepassingen op een HDInsight Spark-cluster verbinden met verschillende soorten gegevens bronnen, zoals Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZEROMQ](http://zeromq.org/), onbewerkte TCP-SOCKETs of door het [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) -bestands systeem te controleren op wijzigingen. Spark streaming ondersteunt fout tolerantie met de garantie dat een bepaalde gebeurtenis precies één keer wordt verwerkt, zelfs als er een storing in een knoop punt optreedt.

Met Spark streaming kunt u langlopende taken maken om trans formaties toe te passen op de gegevens en vervolgens de resultaten te pushen naar bestands systemen, data bases, Dash boards en de-console. Met Spark streaming worden micro batches van gegevens verwerkt, door eerst een batch gebeurtenissen te verzamelen gedurende een opgegeven tijds interval. Vervolgens wordt deze batch verzonden op voor verwerking en uitvoer. Tijds intervallen voor batch worden doorgaans gedefinieerd in fracties van een seconde.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-streaming.png" alt-text="Spark-streaming" border="false":::

## <a name="dstreams"></a>DStreams

Spark streaming vertegenwoordigt een doorlopende stream van gegevens met behulp van een *onderscheiden-stroom* (DStream). Deze DStream kan worden gemaakt op basis van invoer bronnen zoals Event Hubs of Kafka, of door trans formaties toe te passen op een andere DStream. Wanneer een gebeurtenis arriveert bij uw Spark-streaming-toepassing, wordt de gebeurtenis op een betrouw bare manier opgeslagen. Dat wil zeggen dat de gebeurtenis gegevens worden gerepliceerd, zodat meerdere knoop punten er een kopie van hebben. Dit zorgt ervoor dat de storing van een enkel knoop punt niet leidt tot verlies van uw gebeurtenis.

De Spark-kern maakt gebruik van *robuuste gedistribueerde gegevens sets* (rdd's). Rdd's distribueert gegevens over meerdere knoop punten in het cluster, waarbij elk knoop punt de gegevens in het algemeen volledig in het geheugen onderhoudt voor de beste prestaties. Elke RDD vertegenwoordigt gebeurtenissen die worden verzameld over een batch-interval. Wanneer de batch-interval is verstreken, produceert Spark streaming een nieuwe RDD met alle gegevens in dat interval. Deze doorlopende set Rdd's wordt verzameld in een DStream. Een Spark-streaming-toepassing verwerkt de gegevens die zijn opgeslagen in de RDD van elke batch.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-dstream.png" alt-text="Spark-DStream" border="false":::

## <a name="spark-structured-streaming-jobs"></a>Taken in Spark Structured streamen

Spark Structured streaming werd geïntroduceerd in Spark 2,0 als analyse-engine voor gebruik in gestructureerde gegevens van streaming. In Spark Structured streaming wordt gebruikgemaakt van de SparkSQL-engine-Api's. Net als bij Spark-streaming worden de berekeningen door Spark Structured streaming uitgevoerd via continu micro-batches van gegevens. Met Spark Structured streaming wordt een gegevens stroom aangeduid als een invoer tabel met onbeperkte rijen. Dat wil zeggen dat de invoer tabel blijft groeien naarmate er nieuwe gegevens binnenkomen. Deze invoer tabel wordt continu verwerkt door een langlopende query en de resultaten worden naar een uitvoer tabel geschreven.

:::image type="content" source="./media/apache-spark-streaming-high-availability/structured-streaming.png" alt-text="Spark Structured streaming" border="false":::

In structured streaming worden gegevens in het systeem bezorgd en onmiddellijk opgenomen in de invoer tabel. U schrijft query's waarmee bewerkingen worden uitgevoerd op basis van deze invoer tabel. De query uitvoer levert een andere tabel, de tabel met resultaten. De resultaten tabel bevat de resultaten van de query waaruit u gegevens tekent om deze te verzenden naar een externe gegevens opslag, zoals een relationele data base. Met het *trigger interval* wordt de timing ingesteld voor wanneer gegevens worden verwerkt uit de invoer tabel. Gestructureerde streaming verwerkt de gegevens standaard zodra ze binnenkomen. U kunt echter ook de trigger zo configureren dat deze wordt uitgevoerd op een langer interval, zodat de streaminggegevens worden verwerkt in op tijd gebaseerde batches. De gegevens in de tabel met resultaten kunnen worden vernieuwd telkens wanneer er nieuwe gegevens zijn, zodat deze alle uitvoer gegevens bevat, omdat de streaming-query is gestart (*volledige modus*), of alleen de gegevens bevat die nieuw zijn sinds de laatste keer dat de query is verwerkt (*toevoeg modus*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Fout tolerante Spark-streaming-taken maken

Als u een Maxi maal beschik bare omgeving voor uw Spark-streaming-taken wilt maken, begint u met het coderen van uw afzonderlijke taken voor herstel in het geval van een storing. Dergelijke taken die zichzelf herstellen, zijn fout tolerant.

Rdd's hebben verschillende eigenschappen die ondersteuning bieden voor Maxi maal beschik bare en fout tolerante Spark-streaming taken:

* Batches van invoer gegevens die zijn opgeslagen in Rdd's als een DStream worden automatisch gerepliceerd in het geheugen voor fout tolerantie.
* Gegevens die verloren zijn gegaan vanwege een fout in de werk nemers, kunnen worden herberekend op basis van gerepliceerde invoer gegevens op verschillende werk rollen, zolang deze werk knooppunten beschikbaar zijn.
* Snel fout herstel kan binnen één seconde optreden, omdat het herstel van fouten/stragglers plaatsvindt via de berekening in het geheugen.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Precies eenmaal semantiek met Spark streaming

Als u een toepassing wilt maken die elke gebeurtenis eenmaal verwerkt (en slechts één keer), overweeg dan hoe alle systeem punten van de fout opnieuw moeten worden opgestart na een probleem en hoe u gegevens verlies kunt voor komen. Er is precies één keer vereist dat er op geen enkel moment gegevens verloren zijn gegaan en dat de bericht verwerking opnieuw kan worden gestart, ongeacht waar de fout optreedt. Zie [Spark-streaming-taken maken met precies één keer per gebeurtenis verwerking](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark-streaming en Apache Hadoop GARENs

In HDInsight wordt het cluster werk gecoördineerd door *nog een resource-onderhandelaar* (garens). Het ontwerpen van hoge Beschik baarheid voor Spark streaming omvat technieken voor Spark-streaming en voor garen onderdelen.  Hieronder ziet u een voor beeld van een configuratie met GARENs.

:::image type="content" source="./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png" alt-text="GAREN architectuur" border="false":::

In de volgende secties worden ontwerp overwegingen voor deze configuratie beschreven.

### <a name="plan-for-failures"></a>Problemen plannen

Als u een garen configuratie voor hoge Beschik baarheid wilt maken, moet u een mogelijke uitvoerder of een probleem met een stuur programma plannen. Sommige Spark-streaming-taken omvatten ook vereisten voor gegevens garantie waarvoor extra configuratie en installatie nodig is. Een streaming-toepassing kan bijvoorbeeld een zakelijke vereiste hebben voor een garantie van nul gegevens verlies ondanks een fout in het hosting streaming-systeem of het HDInsight-cluster.

Als een  uitvoerder mislukt, worden de bijbehorende taken en ontvangers automatisch opnieuw gestart door Spark, zodat er geen configuratie wijziging nodig is.

Als een **stuur programma** echter niet kan worden uitgevoerd, mislukken alle bijbehorende uitvoerder en gaan alle ontvangen blokken en reken resultaten verloren. Als u de fout wilt herstellen na een stuurprogrammafout, gebruikt u *DStream-controle punten* zoals beschreven in [Spark-streaming-taken maken met precies één keer per gebeurtenis verwerking](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Met DStream controlepunten wordt regel matig de *directed acyclic Graph* (dag) van DStreams opgeslagen op fout tolerante opslag, zoals Azure Storage.  Met controle punten kan met Spark Structured streaming het mislukte stuur programma opnieuw worden gestart door de controlepunt gegevens.  Met dit stuur programma opnieuw opstarten worden nieuwe uitvoerders gestart en worden ontvangers ook opnieuw opgestart.

Stuur Programma's herstellen met DStream controle punt:

* Automatisch opnieuw opstarten van Stuur Programma's op GARENs configureren met de configuratie-instelling `yarn.resourcemanager.am.max-attempts` .
* Een map met een controle punt instellen in een bestands systeem met HDFS-compatibel met `streamingContext.checkpoint(hdfsDirectory)` .
* Bron code herstructureren voor het gebruik van controle punten voor herstel, bijvoorbeeld:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Configureer verloren gegevens herstel door het Write-Ahead logboek (WAL) in te scha kelen `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` en de replicatie in het geheugen voor invoer DStreams uit te scha kelen met `StorageLevel.MEMORY_AND_DISK_SER` .

Als u wilt samenvatten, met behulp van controle punten + WAL + reliable receivers, kunt u ' ten minste eenmaal ' gegevens herstel leveren:

* Slechts eenmaal, zolang de ontvangen gegevens niet verloren zijn gegaan en de uitvoer idempotent of transactioneel zijn.
* Slechts eenmaal, met de nieuwe Kafka direct-benadering, waarbij Kafka als gerepliceerd logboek wordt gebruikt in plaats van ontvangers of WALs te gebruiken.

### <a name="typical-concerns-for-high-availability"></a>Typische problemen met hoge Beschik baarheid

* Het is moeilijker om streaming-taken te bewaken dan batch taken. Spark-streaming-taken worden meestal langlopend en GARENs vormen geen statistische logboeken tot een taak is voltooid.  Spark-controle punten gaan verloren tijdens het bijwerken van de toepassing of Spark, en u moet de map met het controle punt wissen tijdens een upgrade.

* Configureer uw GARENs cluster modus voor het uitvoeren van Stuur Programma's, zelfs als een client uitvalt. Automatisch opnieuw opstarten voor Stuur Programma's instellen:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark en de gebruikers interface van Spark-streaming hebben een configureerbaar metrisch systeem. U kunt ook extra bibliotheken gebruiken, zoals grafiet/Grafana, om de metrische gegevens van het dash board te downloaden, zoals ' num records verwerkte ', ' geheugen/GC-gebruik op Stuur & programma ', ' totale vertraging ', ' gebruik van het cluster ', enzovoort. In structured streaming versie 2,1 of hoger kunt u gebruiken `StreamingQueryListener` om extra metrische gegevens te verzamelen.

* U moet langlopende taken segmenteren.  Wanneer een Spark-streaming-toepassing wordt ingediend bij het cluster, moet de GARENs wachtrij waarin de taak wordt uitgevoerd, worden gedefinieerd. U kunt een [capaciteits planner voor garens](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) gebruiken om langlopende taken te verzenden naar afzonderlijke wacht rijen.

* Sluit uw streaming-toepassing probleemloos af. Als uw offsets bekend zijn, en alle toepassings status extern worden opgeslagen, kunt u de streaming-toepassing op een programmatische manier stoppen op de juiste plaats. Een techniek is het gebruik van ' thread hooks ' in Spark, door elke *n* seconden op een externe vlag te controleren. U kunt ook een *markerings bestand* gebruiken dat is gemaakt op HDFS wanneer u de toepassing start en vervolgens verwijdert wanneer u wilt stoppen. Voor een methode van een markerings bestand gebruikt u een afzonderlijke thread in uw Spark-toepassing die de code aanroept die er ongeveer als volgt uitziet:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Apache Spark streaming](apache-spark-streaming-overview.md)
* [Apache Spark streaming-taken maken met precies eenmaal per gebeurtenis verwerking](apache-spark-streaming-exactly-once.md)
* [Langlopende Apache Spark streaming-taken op GARENs](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Structured streaming: fout tolerante semantiek](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Onderscheiden streams: een Fault-Tolerant model voor schaal bare stroom verwerking](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
