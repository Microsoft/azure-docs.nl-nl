---
title: Toepassings Insight-logboeken analyseren met Spark-Azure HDInsight
description: Meer informatie over het exporteren van Application Insight-logboeken naar Blob-opslag en het analyseren van de logboeken met Spark in HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 9ae06565039e53c0096ff0541f7b28a5b041d9e8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864547"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Application Insights-telemetrie-logboeken analyseren met Apache Spark in HDInsight

Meer informatie over het gebruik van [Apache Spark](https://spark.apache.org/) in HDInsight voor het analyseren van de telemetrie-gegevens van Application Insight.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) is een analyse service waarmee uw webtoepassingen worden bewaakt. Telemetriegegevens die zijn gegenereerd door Application Insights kunnen worden geëxporteerd naar Azure Storage. Zodra de gegevens zijn Azure Storage, kan HDInsight worden gebruikt om deze te analyseren.

## <a name="prerequisites"></a>Vereisten

* Een toepassing die is geconfigureerd voor het gebruik van Application Insights.

* Vertrouwd met het maken van een HDInsight-cluster op basis van Linux. Zie [Apache Spark maken op HDInsight](apache-spark-jupyter-spark-sql.md)voor meer informatie.

* Een webbrowser.

De volgende resources zijn gebruikt bij het ontwikkelen en testen van dit document:

* Application Insights telemetriegegevens zijn gegenereerd met eenNode.js- [ Web-app die is geconfigureerd om Application Insights te gebruiken](../../azure-monitor/app/nodejs.md).

* Er is een op Linux gebaseerde Spark op HDInsight-cluster versie 3,5 gebruikt voor het analyseren van de gegevens.

## <a name="architecture-and-planning"></a>Architectuur en planning

In het volgende diagram ziet u de service architectuur van dit voor beeld:

:::image type="content" source="./media/apache-spark-analyze-application-insight-logs/application-insights.png" alt-text="Gegevens die worden overgelopen van Application Insights naar Blob Storage en vervolgens Spark" border="false":::

### <a name="azure-storage"></a>Azure Storage

Application Insights kan worden geconfigureerd om voortdurend telemetriegegevens naar blobs te exporteren. HDInsight kan vervolgens gegevens lezen die zijn opgeslagen in de blobs. Er zijn echter enkele vereisten die u moet volgen:

* **Locatie**: als het opslag account en HDInsight zich op verschillende locaties bevinden, kan de latentie langer duren. De kosten worden ook verhoogd, omdat er uitvoer kosten worden toegepast op gegevens die tussen regio's worden verplaatst.

    > [!WARNING]  
    > Het gebruik van een opslag account in een andere locatie dan HDInsight wordt niet ondersteund.

* **BLOB-type**: HDInsight ondersteunt alleen blok-blobs. Application Insights wordt standaard ingesteld op het gebruik van blok-blobs, dus moet u standaard werken met HDInsight.

Zie het document [extra opslag accounts toevoegen](../hdinsight-hadoop-add-storage.md) voor meer informatie over het toevoegen van opslag aan een bestaand cluster.

### <a name="data-schema"></a>Gegevens schema

Application Insights biedt informatie over het [exporteren van gegevens model](../../azure-monitor/app/export-data-model.md) voor de telemetriegegevens die zijn geëxporteerd naar blobs. In de stappen in dit document wordt Spark SQL gebruikt voor het werken met de gegevens. Spark SQL kan automatisch een schema genereren voor de JSON-gegevens structuur die is vastgelegd door Application Insights.

## <a name="export-telemetry-data"></a>Telemetriegegevens exporteren

Volg de stappen in [continue export configureren](../../azure-monitor/app/export-telemetry.md) om uw Application Insights te configureren voor het exporteren van telemetriegegevens naar een Azure Storage blob.

## <a name="configure-hdinsight-to-access-the-data"></a>HDInsight configureren voor toegang tot de gegevens

Als u een HDInsight-cluster maakt, voegt u het opslag account toe tijdens het maken van het cluster.

Als u het Azure Storage-account wilt toevoegen aan een bestaand cluster, gebruikt u de informatie in het document [extra opslag accounts toevoegen](../hdinsight-hadoop-add-storage.md) .

## <a name="analyze-the-data-pyspark"></a>De gegevens analyseren: PySpark

1. Navigeer vanuit een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/jupyter` de naam van het cluster.

2. Selecteer in de rechter bovenhoek van de pagina Jupyter **Nieuw** en klik vervolgens op **PySpark**. Er wordt een nieuw browser tabblad met een op python gebaseerd Jupyter Notebook geopend.

3. Voer in het eerste veld (een **cel**) op de pagina de volgende tekst in:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Met deze code wordt Spark zodanig geconfigureerd dat de mapstructuur voor de invoer gegevens recursief wordt geopend. Application Insights telemetrie wordt geregistreerd in een directory structuur die vergelijkbaar is met die van `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Gebruik **SHIFT + ENTER** om de code uit te voeren. Aan de linkerkant van de cel wordt een ' \* ' weer gegeven tussen de vier Kante haken om aan te geven dat de code in deze cel wordt uitgevoerd. Zodra de bewerking is voltooid, wordt de ' \* ' gewijzigd in een getal en wordt de uitvoer vergelijkbaar met de volgende tekst weer gegeven onder de cel:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Er wordt een nieuwe cel gemaakt onder het eerste item. Voer de volgende tekst in de nieuwe cel in. Vervang `CONTAINER` en door `STORAGEACCOUNT` de Azure Storage account naam en de naam van de BLOB-container die Application Insights gegevens bevat.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Gebruik **SHIFT + ENTER** om deze cel uit te voeren. Er wordt een resultaat weer gegeven dat vergelijkbaar is met de volgende tekst:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Het geretourneerde wasbs is de locatie van de Application Insights telemetrie-gegevens. Wijzig de `hdfs dfs -ls` regel in de cel om het geretourneerde wasbs te gebruiken, en gebruik **SHIFT + ENTER** om de cel opnieuw uit te voeren. Deze keer worden de resultaten weer gegeven met de mappen die telemetriegegevens bevatten.

   > [!NOTE]  
   > Voor de rest van de stappen in deze sectie is de `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` map gebruikt. De mapstructuur kan afwijken.

6. Voer in de volgende cel de volgende code in: Vervang door `WASB_PATH` het pad uit de vorige stap.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Met deze code wordt een data frame gemaakt op basis van de JSON-bestanden die zijn geëxporteerd door het doorlopende export proces. Gebruik **SHIFT + ENTER** om deze cel uit te voeren.
7. Voer in de volgende cel en voer de volgende handelingen uit om het schema weer te geven dat Spark heeft gemaakt voor de JSON-bestanden:

   ```python
   jsonData.printSchema()
   ```

    Het schema voor elk type telemetrie wijkt af. Het volgende voor beeld is het schema dat wordt gegenereerd voor webaanvragen (gegevens die zijn opgeslagen in de `Requests` submap):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. Gebruik de volgende opdracht om de data frame te registreren als een tijdelijke tabel en een query uit te voeren op de gegevens:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Met deze query wordt de plaats informatie geretourneerd voor de top 20 records waar context. location. City niet null is.

   > [!NOTE]  
   > De context structuur is aanwezig in alle door Application Insights vastgelegde telemetrie. Het element City kan niet worden ingevuld in uw logboeken. Gebruik het schema om andere elementen te identificeren waarvoor u een query kunt uitvoeren die gegevens voor uw logboeken kan bevatten.

    Deze query retourneert informatie die lijkt op de volgende tekst:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="analyze-the-data-scala"></a>De gegevens analyseren: scala

1. Navigeer vanuit een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/jupyter` de naam van het cluster.

2. Selecteer in de rechter bovenhoek van de pagina Jupyter **Nieuw** en klik vervolgens op **scala**. Er wordt een nieuw browser tabblad met een op scala gebaseerde Jupyter Notebook weer gegeven.

3. Voer in het eerste veld (een **cel**) op de pagina de volgende tekst in:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Met deze code wordt Spark zodanig geconfigureerd dat de mapstructuur voor de invoer gegevens recursief wordt geopend. Application Insights telemetrie wordt geregistreerd in een directory structuur die vergelijkbaar is met `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Gebruik **SHIFT + ENTER** om de code uit te voeren. Aan de linkerkant van de cel wordt een ' \* ' weer gegeven tussen de vier Kante haken om aan te geven dat de code in deze cel wordt uitgevoerd. Zodra de bewerking is voltooid, wordt de ' \* ' gewijzigd in een getal en wordt de uitvoer vergelijkbaar met de volgende tekst weer gegeven onder de cel:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    spark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Er wordt een nieuwe cel gemaakt onder het eerste item. Voer de volgende tekst in de nieuwe cel in. Vervang `CONTAINER` en door `STORAGEACCOUNT` de Azure Storage account naam en de naam van de BLOB-container die Application Insights-Logboeken bevat.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Gebruik **SHIFT + ENTER** om deze cel uit te voeren. Er wordt een resultaat weer gegeven dat vergelijkbaar is met de volgende tekst:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Het geretourneerde wasbs is de locatie van de Application Insights telemetrie-gegevens. Wijzig de `hdfs dfs -ls` regel in de cel om het geretourneerde wasbs te gebruiken, en gebruik **SHIFT + ENTER** om de cel opnieuw uit te voeren. Deze keer worden de resultaten weer gegeven met de mappen die telemetriegegevens bevatten.

   > [!NOTE]  
   > Voor de rest van de stappen in deze sectie is de `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` map gebruikt. Deze map bestaat mogelijk niet, tenzij uw telemetrie-gegevens voor een web-app zijn.

6. Voer in de volgende cel de volgende code in: Vervang door `WASB\_PATH` het pad uit de vorige stap.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Met deze code wordt een data frame gemaakt op basis van de JSON-bestanden die zijn geëxporteerd door het doorlopende export proces. Gebruik **SHIFT + ENTER** om deze cel uit te voeren.

7. Voer in de volgende cel en voer de volgende handelingen uit om het schema weer te geven dat Spark heeft gemaakt voor de JSON-bestanden:

   ```scala
   jsonData.printSchema
   ```

    Het schema voor elk type telemetrie wijkt af. Het volgende voor beeld is het schema dat wordt gegenereerd voor webaanvragen (gegevens die zijn opgeslagen in de `Requests` submap):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. Gebruik de volgende opdracht om de data frame te registreren als een tijdelijke tabel en een query uit te voeren op de gegevens:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Met deze query wordt de plaats informatie geretourneerd voor de top 20 records waar context. location. City niet null is.

   > [!NOTE]  
   > De context structuur is aanwezig in alle door Application Insights vastgelegde telemetrie. Het element City kan niet worden ingevuld in uw logboeken. Gebruik het schema om andere elementen te identificeren waarvoor u een query kunt uitvoeren die gegevens voor uw logboeken kan bevatten.

    Deze query retourneert informatie die lijkt op de volgende tekst:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer voor beelden van het gebruik van Apache Spark voor het werken met gegevens en services in Azure:

* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

Raadpleeg de volgende documenten voor meer informatie over het maken en uitvoeren van Spark-toepassingen:

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Taken op afstand uitvoeren op een Apache Spark cluster met behulp van livy](apache-spark-livy-rest-interface.md)
