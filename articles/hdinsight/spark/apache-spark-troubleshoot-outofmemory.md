---
title: OutOfMemoryError-uitzonde ringen voor Apache Spark in azure HDInsight
description: Verschillende OutOfMemoryError-uitzonde ringen voor Apache Spark cluster in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: dd33972810ab3b0d51bbd82282d0e6cf6cd9d96c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868661"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>OutOfMemoryError-uitzonde ringen voor Apache Spark in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scenario: OutOfMemoryError-uitzonde ring voor Apache Spark

### <a name="issue"></a>Probleem

Uw Apache Spark-toepassing is mislukt met een onverwerkte OutOfMemoryError-uitzonde ring. Er wordt mogelijk een fout bericht met de volgende strekking weer gegeven:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Oorzaak

De meest waarschijnlijke oorzaak van deze uitzonde ring is dat er niet voldoende geheugen voor de heap wordt toegewezen aan de virtuele Java-machines (JVMs). Deze JVMs worden als uitvoerende toepassingen of stuur Programma's geïntroduceerd als onderdeel van de Apache Spark toepassing.

### <a name="resolution"></a>Oplossing

1. Bepaal de maximale grootte van de gegevens die de Spark-toepassing moet verwerken. Maak een schatting van de grootte op basis van het maximum van de invoer gegevens, de tussenliggende gegevens die worden geproduceerd door het transformeren van de invoer gegevens en de uitvoer gegevens die de tussenliggende gegevens hebben getransformeerd. Als de oorspronkelijke schatting niet voldoende is, verg root u de grootte en herhaalt u dit tot de geheugen fouten.

1. Zorg ervoor dat het HDInsight-cluster dat u wilt gebruiken, over voldoende resources (geheugen en kernen) beschikt voor uitvoering van de Spark-toepassing. Dit kan worden bepaald door de sectie cluster metrieken van de garen-gebruikers interface van het cluster te bekijken voor de waarden van **geheugen die worden gebruikt** versus **Totaal geheugen** en **VCores dat is gebruikt** voor het **totaal van VCores**.

    :::image type="content" source="./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png" alt-text="kern geheugen weergave van garen" border="true":::

1. Stel de volgende Spark-configuraties in op de juiste waarden. De toepassings vereisten te verdelen over de beschik bare resources in het cluster. Deze waarden mogen niet groter zijn dan 90% van het beschik bare geheugen en de kernen die door GARENs worden weer gegeven en moet ook voldoen aan de minimale geheugen vereisten van de Spark-toepassing:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Totaal geheugen dat wordt gebruikt door alle uitvoerders =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Totaal geheugen gebruikt door stuur programma =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scenario: fout in Java-heap-ruimte bij het openen van Apache Spark geschiedenis server

### <a name="issue"></a>Probleem

U ontvangt de volgende fout melding bij het openen van gebeurtenissen in de Spark-geschiedenis server:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Oorzaak

Dit probleem wordt vaak veroorzaakt door een gebrek aan resources bij het openen van grote bestanden met Spark-gebeurtenissen. De grootte van de Spark-heap is standaard ingesteld op 1 GB, maar grote Spark-gebeurtenis bestanden kunnen meer dan dit vereisen.

Als u de grootte van de bestanden die u wilt laden wilt controleren, kunt u de volgende opdrachten uitvoeren:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Oplossing

U kunt het Spark-geschiedenis server geheugen verhogen door de `SPARK_DAEMON_MEMORY` eigenschap in de Spark-configuratie te bewerken en alle services opnieuw te starten.

U kunt dit doen vanuit de gebruikers interface van de Ambari-browser door de sectie Spark2/config/Advanced Spark2-env te selecteren.

:::image type="content" source="./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png" alt-text="Sectie Geavanceerde spark2-env" border="true":::

Voeg de volgende eigenschap toe om het Spark-geschiedenis server geheugen van 1g te wijzigen in 4G: `SPARK_DAEMON_MEMORY=4g` .

:::image type="content" source="./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png" alt-text="Spark-eigenschap" border="true":::

Zorg ervoor dat u alle betrokken services opnieuw opstart vanuit Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scenario: livy-server kan niet worden gestart op Apache Spark cluster

### <a name="issue"></a>Probleem

De livy-server kan niet worden gestart op een Apache Spark [(Spark 2,1 op Linux (HDI 3,6)]. Poging tot opnieuw starten van de resultaten in de volgende fouten stack, uit de livy-logboeken:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Oorzaak

`java.lang.OutOfMemoryError: unable to create new native thread` highlight besturings systeem kan niet meer systeem eigen threads toewijzen aan JVMs. Bevestigd dat deze uitzonde ring wordt veroorzaakt door een overschrijding van de limiet van het aantal threads per proces.

Wanneer de livy-server onverwacht wordt beëindigd, worden alle verbindingen met Spark-clusters ook beëindigd, wat betekent dat alle taken en gerelateerde gegevens verloren gaan. In het HDP 2,6-sessie herstel mechanisme is geïntroduceerd, worden de sessie gegevens in Zookeeper opgeslagen om te worden hersteld nadat de livy-server weer is.

Wanneer een groot aantal taken wordt verzonden via livy, worden deze sessie statussen in ZK (op HDInsight-clusters) opgeslagen als onderdeel van hoge Beschik baarheid voor livy-server en worden deze sessies hersteld wanneer de livy-service opnieuw wordt gestart. Wanneer de computer opnieuw wordt opgestart na een onverwachte beëindiging, maakt livy één thread per sessie. Dit is een aantal te herstellen sessies die te veel threads veroorzaken.

### <a name="resolution"></a>Oplossing

Verwijder alle vermeldingen met behulp van de stappen die hieronder worden beschreven.

1. Het IP-adres van de Zookeeper-knoop punten ophalen met

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. De bovenstaande opdracht vermeldt alle zookeepers voor mijn cluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Haal het IP-adres van de Zookeeper-knoop punten op met ping, maar u kunt ook verbinding maken met Zookeeper vanuit hoofd knooppunt met behulp van ZK-naam

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Zodra u verbinding hebt gemaakt met Zookeeper, voert u de volgende opdracht uit om een lijst weer te geven met alle sessies die opnieuw moeten worden gestart.

    1. In de meeste gevallen is dit een lijst met meer dan 8000 sessies ####

        ```bash
        ls /livy/v1/batch
        ```

    1. U kunt de volgende opdracht volgen om alle herstelde sessies te verwijderen. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Wacht tot de bovenstaande opdracht is voltooid en de cursor de prompt retour neren en start de livy-service vervolgens opnieuw vanaf Ambari. deze moet slagen.

> [!NOTE]
> `DELETE` de livy-sessie zodra de uitvoering is voltooid. De livy-batch sessies worden niet automatisch verwijderd zodra de Spark-app is voltooid. Dit is een ontwerp. Een livy-sessie is een entiteit die is gemaakt met een POST-aanvraag voor livy rest server. Er `DELETE` is een aanroep nodig om die entiteit te verwijderen. Of we moeten wachten tot de GC is gestart.

---

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* [Overzicht van Spark-geheugen beheer](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Fout opsporing van Spark-toepassing op HDInsight-clusters](/archive/blogs/azuredatalake/spark-debugging-101).

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).