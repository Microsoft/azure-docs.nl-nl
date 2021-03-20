---
title: Data Science met behulp van scala en Spark op Azure-team data Science process
description: Hoe u scala kunt gebruiken voor onder Super visie gemachine learning taken met de mousserend schaal bare MLlib en Spark ML-pakketten op een Azure HDInsight Spark cluster.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9ae4549fe343422bbf60275a97768ca407f2dc7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321366"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Gegevenswetenschap met Scala en Spark op Azure
Dit artikel laat u zien hoe u scala gebruikt voor onder Super visie machine learning taken met de mousserend schaal bare MLlib en Spark ML-pakketten op een Azure HDInsight Spark cluster. U wordt begeleid bij de taken die het [Data Science proces](./index.yml)vormen: gegevens opname en exploratie, visualisatie, functie techniek, model lering en model verbruik. De modellen in het artikel zijn onder andere logistiek en lineaire regressie, wille keurige forests en GBTs (Gradient-boosted Trees), naast twee algemene gecontroleerde machine learning taken:

* Regressie probleem: voor spelling van het fooien bedrag ($) voor een taxi reis
* Binaire classificatie: voor spelling van tip of zonder fooien (1/0) voor een taxi

Voor het model proces zijn training en evaluatie vereist voor een test gegevensset en relevante nauw keurige meet gegevens. In dit artikel vindt u informatie over het opslaan van deze modellen in Azure Blob-opslag en het beoordelen en evalueren van de voorspellende prestaties. In dit artikel worden ook de meer geavanceerde onderwerpen besproken over het optimaliseren van modellen door Kruis validatie en Hyper-para meter sweep te gebruiken. De gebruikte gegevens zijn een voor beeld van de 2013-NYC voor de taxi en de ritbedrag gegevens die beschikbaar zijn op GitHub.

[Scala](https://www.scala-lang.org/), een taal op basis van de virtuele Java-machine, integreert de concepten van object georiënteerde en functionele talen. Het is een schaal bare taal die geschikt is voor gedistribueerde verwerking in de Cloud en wordt uitgevoerd op Azure Spark-clusters.

[Spark](https://spark.apache.org/) is een open-source framework voor parallelle verwerking dat ondersteuning biedt voor in-Memory verwerking om de prestaties van Big Data Analytics-toepassingen te verbeteren. De Spark-verwerkings engine is gebouwd voor snelheid, gebruiks gemak en geavanceerde analyses. Met de in-Memory gedistribueerde reken kundige verwerkings mogelijkheden van Spark is het een goede keuze voor iteratieve algoritmen in machine learning-en grafiek berekeningen. Het [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) -pakket biedt een uniforme set api's op hoog niveau die zijn gebaseerd op gegevens frames die u kunnen helpen bij het maken en afstemmen van praktische machine learning pijp lijnen. [MLlib](https://spark.apache.org/mllib/) is een schaalbaar machine learning-bibliotheek van Spark, waarmee model mogelijkheden naar deze gedistribueerde omgeving worden verplaatst.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) is het Azure-gehoste aanbod van open-source Spark. Het biedt ook ondersteuning voor Jupyter scala-notebooks op het Spark-cluster en kan in Spark SQL Interactive-query's uitvoeren om gegevens die zijn opgeslagen in Azure Blob Storage te transformeren, te filteren en te visualiseren. De scala code fragmenten in dit artikel die de oplossingen bieden en de relevante waarnemings punten weer geven om de gegevens die worden uitgevoerd in Jupyter-notebooks die zijn geïnstalleerd op de Spark-clusters te visualiseren. De stappen voor model lering in deze onderwerpen bevatten code die laat zien hoe u elk type model kunt trainen, evalueren, opslaan en gebruiken.

De installatie stappen en code in dit artikel zijn voor Azure HDInsight 3,4 Spark 1,6. De code in dit artikel en in de scala- [Jupyter notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) zijn echter algemeen en moeten worden gebruikt in een Spark-cluster. De stappen voor het instellen en beheren van het cluster kunnen enigszins afwijken van wat er in dit artikel wordt weer gegeven als u geen gebruik maakt van HDInsight Spark.

> [!NOTE]
> Voor een onderwerp waarin wordt uitgelegd hoe u python gebruikt in plaats van scala voor het volt ooien van taken voor een end-to-end data Science-proces, raadpleegt u [Data Wetenschappen met behulp van Spark in azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Vereisten
* U hebt een abonnement op Azure nodig. Als u er nog geen hebt, kunt u [een gratis proef versie van Azure aanschaffen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* U hebt een Azure HDInsight 3,4 Spark 1,6-cluster nodig om de volgende procedures uit te voeren. Zie de instructies in aan de [slag: create Apache Spark op Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)om een cluster te maken. Stel het cluster type en de versie in op het menu **cluster type selecteren** .

![Configuratie van het HDInsight-cluster type](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Zie voor een beschrijving van de NYC en instructies over het uitvoeren van code uit een Jupyter-notebook op het Spark-cluster de relevante secties in [overzicht van data Science met behulp van Spark in azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Scala-code uitvoeren vanuit een Jupyter-notebook in het Spark-cluster
U kunt een Jupyter-notebook starten vanuit het Azure Portal. Zoek het Spark-cluster op uw dash board en klik erop om de beheer pagina voor uw cluster in te voeren. Klik vervolgens op **cluster dashboards** en klik vervolgens op **Jupyter notebook** om het notitie blok te openen dat is gekoppeld aan het Spark-cluster.

![Cluster dashboard en Jupyter-notebooks](./media/scala-walkthrough/spark-jupyter-on-portal.png)

U hebt ook toegang tot Jupyter-notebooks op https:// &lt; clustername &gt; . azurehdinsight.net/jupyter. Vervang *clustername* door de naam van uw cluster. U hebt het wacht woord voor uw beheerders account nodig om toegang te krijgen tot de Jupyter-notebooks.

![Ga naar Jupyter-notebooks met behulp van de cluster naam](./media/scala-walkthrough/spark-jupyter-notebook.png)

Selecteer **scala** om een map weer te geven met een aantal voor beelden van geverpakkinge notitie blokken die gebruikmaken van de PYSPARK-API. De beoordelings-en Score functies voor verkennen met behulp van de scala. ipynb-notebook met de code voorbeelden voor dit pakket met Spark-onderwerpen is beschikbaar op [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

U kunt het notitie blok rechtstreeks vanuit GitHub uploaden naar de Jupyter Notebook-server op uw Spark-cluster. Klik op de start pagina van Jupyter op de knop **uploaden** . Plak in de Verkenner de GitHub-URL (onbewerkte inhoud) van de scala-notebook en klik vervolgens op **openen**. De scala-notebook is beschikbaar op de volgende URL:

[Exploratie-modelleer-en-Score-using-scala. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Installatie: vooraf ingestelde Spark-en Hive-contexten, Spark-magics en Spark-bibliotheken
### <a name="preset-spark-and-hive-contexts"></a>Vooraf ingestelde Spark-en Hive-contexten

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

De Spark-kernels die worden meegeleverd met Jupyter-notebooks bevatten vooraf ingestelde contexten. U hoeft de Spark-of Hive-contexten niet expliciet in te stellen voordat u aan de slag gaat met de toepassing die u ontwikkelt. De vooraf ingestelde contexten zijn:

* `sc` voor SparkContext
* `sqlContext` voor HiveContext

### <a name="spark-magics"></a>Spark-magics
De Spark-kernel biedt enkele vooraf gedefinieerde ' magics '. Dit zijn speciale opdrachten waarmee u kunt aanroepen `%%` . Twee van deze opdrachten worden gebruikt in de volgende code voorbeelden.

* `%%local` geeft aan dat de code in de volgende regels lokaal wordt uitgevoerd. De code moet een geldige scala-code zijn.
* `%%sql -o <variable name>` voert een Hive-query uit op basis van `sqlContext` . Als de `-o` para meter wordt door gegeven, wordt het resultaat van de query persistent gemaakt in de `%%local` scala-context als een Spark-gegevens frame.

Voor meer informatie over de kernels voor Jupyter-notebooks en hun vooraf gedefinieerde ' magics ' die u aanroept `%%` (bijvoorbeeld `%%local` ), raadpleegt u de [kernels die beschikbaar zijn voor Jupyter-notebooks met hdinsight Spark Linux-clusters in hdinsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Bibliotheken importeren
Importeer de Spark, MLlib en andere bibliotheken die u nodig hebt met behulp van de volgende code.

```scala
# IMPORT SPARK AND JAVA LIBRARIES
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.functions._
import java.text.SimpleDateFormat
import java.util.Calendar
import sqlContext.implicits._
import org.apache.spark.sql.Row

# IMPORT SPARK SQL FUNCTIONS
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
import org.apache.spark.sql.functions.rand

# IMPORT SPARK ML FUNCTIONS
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

# IMPORT SPARK MLLIB FUNCTIONS
import org.apache.spark.mllib.linalg.{Vector, Vectors}
import org.apache.spark.mllib.util.MLUtils
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

# SPECIFY SQLCONTEXT
val sqlContext = new SQLContext(sc)
```

## <a name="data-ingestion"></a>Gegevensopname
De eerste stap in het data Science proces is het opnemen van de gegevens die u wilt analyseren. U brengt de gegevens uit externe bronnen of systemen waar deze zich bevinden in uw gegevens exploratie-en model omgeving. In dit artikel zijn de gegevens die u opneemt een gecombineerd 0,1%-voor beeld van de taxi en het ritbedrag-bestand (opgeslagen als een TSV-bestand). De omgeving voor het verkennen van gegevens en de model lering is Spark. Deze sectie bevat de code voor het uitvoeren van de volgende reeks taken:

1. Directory paden instellen voor gegevens en model opslag.
2. Lees in de gegevensset voor invoer (opgeslagen als een TSV-bestand).
3. Definieer een schema voor de gegevens en schoon de gegevens op.
4. Maak een gereinigd gegevens frame en cache het in het geheugen.
5. Registreer de gegevens als een tijdelijke tabel in SQLContext.
6. Query's uitvoeren op de tabel en de resultaten importeren in een gegevens frame.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Mappaden instellen voor opslag locaties in Azure Blob-opslag
Spark kan lezen van en schrijven naar Azure Blob-opslag. U kunt Spark gebruiken voor het verwerken van uw bestaande gegevens en de resultaten vervolgens opnieuw opslaan in Blob Storage.

Als u modellen of bestanden in Blob Storage wilt opslaan, moet u het pad juist opgeven. Raadpleeg de standaard container die aan het Spark-cluster is gekoppeld met behulp van een pad dat begint met `wasb:///` . Naar andere locaties verwijzen met behulp van `wasb://` .

In het volgende code voorbeeld geeft u de locatie op van de invoer gegevens die moeten worden gelezen en het pad naar de Blob-opslag die is gekoppeld aan het Spark-cluster waar het model wordt opgeslagen.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Gegevens importeren, een RDD maken en een gegevens frame definiëren volgens het schema

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
val sqlContext = new SQLContext(sc)
val taxi_schema = StructType(
    Array(
        StructField("medallion", StringType, true),
        StructField("hack_license", StringType, true),
        StructField("vendor_id", StringType, true),
        StructField("rate_code", DoubleType, true),
        StructField("store_and_fwd_flag", StringType, true),
        StructField("pickup_datetime", StringType, true),
        StructField("dropoff_datetime", StringType, true),
        StructField("pickup_hour", DoubleType, true),
        StructField("pickup_week", DoubleType, true),
        StructField("weekday", DoubleType, true),
        StructField("passenger_count", DoubleType, true),
        StructField("trip_time_in_secs", DoubleType, true),
        StructField("trip_distance", DoubleType, true),
        StructField("pickup_longitude", DoubleType, true),
        StructField("pickup_latitude", DoubleType, true),
        StructField("dropoff_longitude", DoubleType, true),
        StructField("dropoff_latitude", DoubleType, true),
        StructField("direct_distance", StringType, true),
        StructField("payment_type", StringType, true),
        StructField("fare_amount", DoubleType, true),
        StructField("surcharge", DoubleType, true),
        StructField("mta_tax", DoubleType, true),
        StructField("tip_amount", DoubleType, true),
        StructField("tolls_amount", DoubleType, true),
        StructField("total_amount", DoubleType, true),
        StructField("tipped", DoubleType, true),
        StructField("tip_class", DoubleType, true)
        )
    )

# CAST VARIABLES ACCORDING TO THE SCHEMA
val taxi_temp = (taxi_train_file.map(_.split("\t"))
                        .filter((r) => r(0) != "medallion")
                         .map(p => Row(p(0), p(1), p(2),
                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


# CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

# CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Uitvoer**

Tijd voor het uitvoeren van de cel: 8 seconden.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Query uitvoeren op de tabel en resultaten importeren in een gegevens frame
Vervolgens moet u een query uitvoeren op de tabel voor de gegevens voor ritbedrag, reizigers en fooien; beschadigde en begrote gegevens filteren. en afdrukken van verschillende rijen.

```scala
# QUERY THE DATA
val sqlStatement = """
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train
    WHERE passenger_count > 0 AND passenger_count < 7
    AND fare_amount > 0 AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 AND tip_amount < 25
"""
val sqlResultsDF = sqlContext.sql(sqlStatement)

# SHOW ONLY THE TOP THREE ROWS
sqlResultsDF.show(3)
```

**Uitvoer**

| fare_amount | passenger_count | tip_amount | gekanteld |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16,0 |2,0 |3.4 |1.0 |
|        10.5 |2,0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Gegevens verkennen en visualisatie
Nadat u de gegevens in Spark hebt gebracht, is de volgende stap in het data Science-proces het verkrijgen van meer inzicht in de gegevens door te verkennen en visualisatie. In deze sectie onderzoekt u de taxi gegevens met behulp van SQL-query's. Importeer vervolgens de resultaten in een gegevens frame om de doel variabelen en de potentiële functies voor visuele inspectie uit te zetten met behulp van de functie voor automatische visualisatie Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Lokale en SQL Magic gebruiken om gegevens af te zetten
Standaard is de uitvoer van code fragmenten die u vanuit een Jupyter-notebook uitvoert, beschikbaar in de context van de sessie die op de worker-knoop punten wordt gehandhaafd. Als u voor elke berekening een reis wilt opslaan naar de worker-knoop punten en als alle gegevens die u nodig hebt voor uw berekening, lokaal beschikbaar zijn op het knoop punt Jupyter server (dit is het hoofd knooppunt), kunt u het `%%local` Magic gebruiken om het code fragment op de Jupyter-server uit te voeren.

* **SQL Magic** ( `%%sql` ). De HDInsight Spark-kernel ondersteunt eenvoudige inline HiveQL-query's tegen SQLContext. Het `-o VARIABLE_NAME` argument () persistent de uitvoer van de SQL-query als een Panda-gegevens frame op de Jupyter-server. Deze instelling betekent dat de uitvoer beschikbaar is in de lokale modus.
* `%%local`**Magic**. `%%local`Met Magic wordt de code lokaal uitgevoerd op de Jupyter-server. Dit is het hoofd knooppunt van het HDInsight-cluster. Normaal gesp roken gebruikt u `%%local` Magic in combi natie met het `%%sql` Magic met de `-o` para meter. Met de `-o` para meter wordt de uitvoer van de SQL-query lokaal opgeslagen en vervolgens wordt `%%local` de volgende set code fragment lokaal uitgevoerd op basis van de uitvoer van de SQL-query's die lokaal blijven bestaan.

### <a name="query-the-data-by-using-sql"></a>Query's uitvoeren op de gegevens met behulp van SQL
Met deze query worden de taxi-reizen opgehaald op basis van het ritbedrag, het aantal reizigers en het fooien bedrag.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

In de volgende code maakt het `%%local` Magic een lokaal gegevens frame, sqlResults. U kunt sqlResults gebruiken om uit te zetten met behulp van matplotlib.

> [!TIP]
> Local Magic wordt meerdere keren gebruikt in dit artikel. Als uw gegevensset groot is, kunt u het voor beeld maken van een gegevens frame dat past in het lokale geheugen.
> 
> 

### <a name="plot-the-data"></a>De gegevens uitzetten
U kunt uitzetten met behulp van python-code nadat het gegevens frame zich in de lokale context bevindt als een Panda-gegevens frame.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 De Spark-kernel visualiseert automatisch de uitvoer van SQL-query's (HiveQL) nadat u de code hebt uitgevoerd. U kunt kiezen uit verschillende soorten visualisaties:

* Tabel
* Cirkeldiagram
* Lijn
* Gebied
* Staaf

Hier volgt de code voor het uitzetten van de gegevens:

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# PLOT TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.show()
```

**Uitvoer**

![Histogram met fooien](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Hoogte van fooi per aantal passagiers](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Hoogte van fooi per ritbedrag](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Functies maken en functies transformeren en vervolgens gegevens voor invoer in model functies voorbereiden
Voor modellen die zijn gebaseerd op een structuur van Spark ML en MLlib, moet u het doel en de functies voorbereiden met behulp van verschillende technieken, zoals binning, indexering, One-Hot encoding en vectorization. Dit zijn de procedures die in deze sectie moeten worden gevolgd:

1. Maak een nieuwe functie door **binning** uur in tijds verzamelingen voor verkeer.
2. Pas **indexering en één Hot encoding** toe op categorische-functies.
3. **Steek proef en Splits de gegevensset** in trainings-en test breuken.
4. **Geef de variabele en functies** voor de training op en maak een geïndexeerde of een hot-rdd's code ring en test invoer met de naam flexibel gedistribueerde gegevens sets () of gegevens frames.
5. **Categoriseer en vectorize functies en doelen** automatisch om te gebruiken als invoer voor machine learning modellen.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Een nieuwe functie maken door tijdstippen van ophalen toe te wijzen aan tariefperioden
Deze code laat zien hoe u een nieuwe functie van binning uur maakt in tijds verzamelingen voor verkeer en hoe het resulterende gegevens frame in het geheugen moet worden opgeslagen. Wanneer Rdd's-en data-frames herhaaldelijk worden gebruikt, wordt het in de cache plaatsen van prestaties tot betere uitvoerings tijden. Daarom kunt u Rdd's en gegevens frames in verschillende fasen in de volgende procedures in de cache opslaan.

```scala
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
val sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train
"""
val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexeren en één Hot code ring van categorische-functies
De functies van MLlib voor modelleren en voorspellen, vereisen dat functies met categorische invoergegevens worden geïndexeerd of gecodeerd voordat ze worden gebruikt. In deze sectie wordt beschreven hoe u categorische-functies indexeert of versleutelt voor invoer in de modelleer functies.

U moet uw modellen op verschillende manieren indexeren of coderen, afhankelijk van het model. Voor logistiek-en lineaire regressie modellen is bijvoorbeeld één Hot encoding vereist. Zo kan een functie met drie categorieën worden uitgebreid in drie functie kolommen. Elke kolom zou 0 of 1 moeten bevatten, afhankelijk van de categorie van een waarneming. MLlib biedt de functie [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) voor code ring met één Hot-codering. Met dit coderings programma wordt een kolom met label indexen toegewezen aan een kolom met binaire vectoren met Maxi maal één waarde. Met deze code ring kunnen algoritmen die numerieke functies verwachten, zoals logistiek regressie, worden toegepast op categorische-functies.

Hier kunt u slechts vier variabelen transformeren om voor beelden weer te geven. Dit zijn teken reeksen. U kunt ook andere variabelen indexeren, zoals de weekdag, vertegenwoordigd door numerieke waarden, als Categorische-variabelen.

`StringIndexer()`Gebruik functies van MLlib voor het indexeren, gebruiken en voor het gebruik van één Hot encoding `OneHotEncoder()` . Dit is de code voor het indexeren en coderen van categorische-functies:

```scala
# CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# INDEX AND ENCODE VENDOR_ID
val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
val encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
val indexed = stringIndexer.transform(encoded1)
val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
val encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
val indexed = stringIndexer.transform(encoded2)
val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
val encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
val indexed = stringIndexer.transform(encoded3)
val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
val encodedFinal = encoder.transform(indexed)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Uitvoer**

Tijd voor het uitvoeren van de cel: 4 seconden.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Steek proef en Splits de gegevensset in trainingen en test fracties
Deze code maakt een wille keurige steek proef van de gegevens (25%, in dit voor beeld). Hoewel er geen steek proef is vereist voor dit voor beeld als gevolg van de grootte van de gegevensset, laat het artikel u zien hoe u kunt steek proeven, zodat u weet hoe u het moet gebruiken voor uw eigen problemen wanneer dat nodig is. Wanneer voor beelden groot zijn, kan dit aanzienlijke tijd besparen tijdens het trainen van modellen. Splits vervolgens het voor beeld in een trainings onderdeel (75%, in dit voor beeld) en een test onderdeel (25%, in dit voor beeld) voor gebruik in de classificatie en regressie modellen.

Voeg een wille keurig getal (tussen 0 en 1) toe aan elke rij (in de kolom ' ASELECT ') die kan worden gebruikt om tijdens de training Kruis validatie vouwen te selecteren.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
val samplingFraction = 0.25;
val trainingFraction = 0.75;
val testingFraction = (1-trainingFraction);
val seed = 1234;
val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
val sampledDFcount = encodedFinalSampledTmp.count().toInt

val generateRandomDouble = udf(() => {
    scala.util.Random.nextDouble
})

# ADD A RANDOM NUMBER FOR CROSS-VALIDATION
val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

# SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
# INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
val trainData = splits(0)
val testData = splits(1)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Uitvoer**

Tijd voor het uitvoeren van de cel: 2 seconden.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Geef de variabelen en functies van de training op en maak een geïndexeerde of een hot-rdd's code ring en test invoer met het label punt of gegevens frames
Deze sectie bevat code waarmee u kunt zien hoe u categorische tekst gegevens indexeert als een gelabeld punt-gegevens type, en deze te coderen, zodat u er MLlib logistiek-regressie en andere classificatie modellen mee kan trainen en testen. Gelabelde punt objecten zijn Rdd's die zijn ingedeeld op een manier die is vereist als invoer gegevens door de meeste machine learning-algoritmen in MLlib. Een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, hetzij dense of sparse, die is gekoppeld aan een label/respons.

In deze code geeft u de doel variabele (afhankelijke) en de functies op die moeten worden gebruikt voor het trainen van modellen. Vervolgens maakt u een geïndexeerde of een hot-rdd's code ring en voert u invoer met het label punt of gegevens frames.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

# CREATE INDEXED LABELED POINT RDD OBJECTS
val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

# CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
val indexedTESTbinaryDF = indexedTESTbinary.toDF()
val indexedTRAINregDF = indexedTRAINreg.toDF()
val indexedTESTregDF = indexedTESTreg.toDF()

# CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
val OneHotTRAIN = assemblerOneHot.transform(trainData)
val OneHotTEST = assemblerOneHot.transform(testData)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Uitvoer**

Tijd voor het uitvoeren van de cel: 4 seconden.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Functies en doelen van vectorize automatisch categoriseren en gebruiken als invoer voor machine learning modellen
Gebruik Spark ML om het doel en de functies te categoriseren die moeten worden gebruikt in op structuur gebaseerde modelleer functies. De code voert twee taken uit:

* Hiermee maakt u een binair doel voor de classificatie door een waarde van 0 of 1 toe te wijzen aan elk gegevens punt tussen 0 en 1 met behulp van een drempel waarde van 0,5.
* De functies worden automatisch gecategoriseerd. Als het aantal afzonderlijke numerieke waarden voor een functie kleiner is dan 32, wordt die functie gecategoriseerd.

Hier volgt de code voor deze twee taken.

```scala
# CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
# CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINregDF)
val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binair classificatie model: voor spelt of een tip moet worden betaald
In deze sectie maakt u drie soorten binaire classificatie modellen om te voors pellen of een tip moet worden betaald:

* Een **logistiek regressie model** met behulp van de Spark ml- `LogisticRegression()` functie
* Een **wille keurig forest-classificatie model** met behulp van de Spark ml- `RandomForestClassifier()` functie
* Een **kleur overgang** voor het verhogen van de boom structuur met behulp van de `GradientBoostedTrees()` functie MLlib

### <a name="create-a-logistic-regression-model"></a>Een logistiek regressiemodel maken
Maak vervolgens een logistiek regressie model met behulp van de Spark ML- `LogisticRegression()` functie. U maakt de code voor het maken van het model in een reeks stappen:

1. **Train de model** gegevens met één parameterset.
2. **Evalueer het model** op een test gegevensset met metrische gegevens.
3. **Sla het model** op in Blob Storage voor toekomstig gebruik.
4. **Het model** beoordelen op basis van test gegevens.
5. **De resultaten uitzetten met de** (ROC) curven van de ontvanger.

Hier volgt de code voor deze procedures:

```scala
# CREATE A LOGISTIC REGRESSION MODEL
val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
val lrModel = lr.fit(OneHotTRAIN)

# PREDICT ON THE TEST DATA SET
val predictions = lrModel.transform(OneHotTEST)

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)

# SAVE THE MODEL
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LogisticRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);
```

Laad, Score en sla de resultaten op.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON THE TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
predictions.registerTempTable("testResults")

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC RESULTS
println("ROC on test data = " + ROC)
```

**Uitvoer**

ROC op test gegevens = 0.9827381497557599

Gebruik python op lokale Panda-gegevens frames om de ROC-curve uit te zetten.

```scala
# QUERY THE RESULTS
%%sql -q -o sqlResults
SELECT tipped, probability from testResults


# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
predictions_pddf = sqlResults[["tipped","probFloat"]]

# PREDICT THE ROC CURVE
# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
prob = predictions_pddf["probFloat"]
fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT THE ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**Uitvoer**

![Tip of geen tip ROC-curve](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Een wille keurig forest-classificatie model maken
Maak vervolgens een wille keurig forest-classificatie model met behulp van de Spark ML `RandomForestClassifier()` -functie en evalueer vervolgens het model op test gegevens.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE THE RANDOM FOREST CLASSIFIER MODEL
val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

# FIT THE MODEL
val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

# EVALUATE THE MODEL
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(predictions)
println("F1 score on test data: " + Test_f1Score);

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)
```

**Uitvoer**

ROC op test gegevens = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Een GBT-classificatie model maken
Maak vervolgens een GBT-classificatie model met behulp van de functie van MLlib `GradientBoostedTrees()` en evalueer vervolgens het model op test gegevens.

```scala
# TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE GBT CLASSIFICATION MODEL
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 20
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

# TRAIN THE MODEL
val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

# SAVE THE MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "GBT_Classification__"
val filename = modelDir.concat(modelName).concat(datestamp)
gbtModel.save(sc, filename);

# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
val labelAndPreds = indexedTESTbinary.map { point =>
  val prediction = gbtModel.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
println("Test Error = " + testErr)

# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
val metrics = new MulticlassMetrics(labelAndPreds)
println(s"Precision: ${metrics.precision}")
println(s"Recall: ${metrics.recall}")
println(s"F1 Score: ${metrics.fMeasure}")

val metrics = new BinaryClassificationMetrics(labelAndPreds)
println(s"Area under PR curve: ${metrics.areaUnderPR}")
println(s"Area under ROC curve: ${metrics.areaUnderROC}")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC METRIC
println(s"Area under ROC curve: ${metrics.areaUnderROC}")
```

**Uitvoer**

Gebied onder ROC-curve: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressie model: aantal voor speld fooien
In deze sectie maakt u twee typen regressie modellen voor het voors pellen van het fooien aantal:

* Een **geregeld lineair regressie model** met behulp van de Spark ml- `LinearRegression()` functie. U slaat het model op en evalueert het model op test gegevens.
* Een **verloop-regressie model voor het herroten** van de structuur met behulp van de Spark ml- `GBTRegressor()` functie.

### <a name="create-a-regularized-linear-regression-model"></a>Een geregeld lineair regressie model maken

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

# FIT THE MODEL BY USING DATA FRAMES
val lrModel = lr.fit(OneHotTRAIN)
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")

# SAVE THE MODEL IN AZURE BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LinearRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);

# PRINT THE COEFFICIENTS
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = lrModel.transform(OneHotTEST)

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```


**Uitvoer**

Tijd om de cel uit te voeren: 13 seconden.

```scala
# LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
predictions.registerTempTable("testResults")

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("R-sqr on test data = " + r2)
```

**Uitvoer**

R-Sqr op test gegevens = 0.5960320470835743

Voer vervolgens een query uit op de test resultaten als een gegevens frame en gebruik AutoVizWidget en matplotlib om het te visualiseren.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

Met de code wordt een lokaal gegevens frame gemaakt op basis van de uitvoer van de query en worden de gegevens getekend. `%%local`Met Magic maakt u een lokaal gegevens frame, `sqlResults` dat u kunt gebruiken om met matplotlib te tekenen.

> [!NOTE]
> Deze Spark Magic wordt meerdere keren gebruikt in dit artikel. Als de hoeveelheid gegevens groot is, kunt u het beste een voor beeld maken van een gegevens frame dat past in het lokale geheugen.
> 
> 

Maak grafieken met behulp van python matplotlib.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
sqlResults
%matplotlib inline
import numpy as np

# PLOT THE RESULTS
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 8])
plt.show(ax)
```

**Uitvoer**

![Aantal fooien: werkelijk versus voor speld](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Een GBT-regressie model maken
Maak een GBT regressie model met behulp van de Spark ML `GBTRegressor()` -functie en evalueer vervolgens het model op test gegevens.

GBTS ( [Gradient-boosted trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) zijn ensembles van beslissings structuren. GBTS traint een herhaling van de beslissings structuren om een verlies functie te minimaliseren. U kunt GBTS gebruiken voor regressie en classificatie. Ze kunnen categorische-functies afhandelen, geen functie schaaling vereisen en niet-lineaire en functie-interacties vastleggen. U kunt ze ook gebruiken in een instelling voor een classificatie met een hoge klasse.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# TRAIN A GBT REGRESSION MODEL
val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

# MAKE PREDICTIONS
val predictions = gbtModel.transform(indexedTESTwithCatFeat)

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(predictions)


# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("Test R-sqr is: " + Test_R2);
```

**Uitvoer**

Test R-Sqr is: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Geavanceerde hulpprogram ma's voor model lering voor optimalisatie
In deze sectie gebruikt u machine learning-hulpprogram ma's die ontwikkel aars vaak gebruiken voor model optimalisatie. Met name kunt u machine learning modellen op drie verschillende manieren optimaliseren door gebruik te maken van para meters voor opruimen en kruis validatie:

* De gegevens in de trein-en validatie sets splitsen, het model optimaliseren met behulp van Hyper-para meters voor een Trainingsset en evalueren op basis van een validatieset (lineaire regressie)
* Optimaliseer het model met behulp van kruis validatie en Hyper-para meter sweep met behulp van de CrossValidator-functie van Spark ML (binaire classificatie)
* Optimaliseer het model met behulp van aangepaste code voor kruis validatie en het opruimen van para meters voor het gebruik van een machine learning functie en parameter set (lineaire regressie)

**Kruis validatie** is een techniek waarmee wordt beoordeeld hoe goed een model dat is getraind op een bekende set gegevens, wordt gegeneraliseerd om de functies te voors pellen van gegevens sets waarvoor het niet is getraind. Het algemene idee achter deze techniek is dat een model wordt getraind op basis van een gegevens reeks van bekende gegevens en vervolgens de nauw keurigheid van de voor spellingen wordt getest op een onafhankelijke gegevensset. Een veelvoorkomende implementatie is het opsplitsen van een gegevensset in *k*-vouwen en het model vervolgens op een Round-Robin op alle, maar een van de vouwen te trainen.

**Optimalisatie van Hyper-para meters** is het probleem bij het kiezen van een set Hyper-para meters voor een leer algoritme, meestal met het doel om een meting van de prestaties van het algoritme op een onafhankelijke gegevensset te optimaliseren. Een Hyper-para meter is een waarde die u buiten de model trainings procedure moet opgeven. Veronderstellingen over waarden voor Hyper-para meters kunnen van invloed zijn op de flexibiliteit en nauw keurigheid van het model. Beslissings structuren hebben Hyper-para meters, bijvoorbeeld zoals de gewenste diepte en het aantal bladeren in de boom structuur. U moet een niet-geclassificeerde sanctie term instellen voor een ondersteunings vector machine (SVM).

Een veelgebruikte manier om optimalisatie van Hyper-para meters uit te voeren is het gebruik van een raster zoekactie, ook wel het **opruimen van para** meters. In een raster zoekopdracht wordt een uitgebreide zoek opdracht uitgevoerd via de waarden van een opgegeven subset van de Hyper-parameter ruimte voor een leer algoritme. Kruis validatie kan een prestatie metriek bieden om de optimale resultaten te sorteren die worden geproduceerd door het zoek algoritme voor rasters. Als u de functie voor het opruimen van Hyper-para meters Kruis validatie gebruikt, kunt u problemen ondervinden zoals het aanpassen van een model op trainings gegevens. Op deze manier behoudt het model de capaciteit die moet worden toegepast op de algemene set gegevens waaruit de trainings gegevens zijn geëxtraheerd.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Een lineair regressie model optimaliseren met Hyper-para meters opruimen
Vervolgens splitst u gegevens in de trein-en validatie sets, maakt u gebruik van Hyper-para meters voor een Trainingsset om het model te optimaliseren en evalueert u een valideringsset (lineaire regressie).

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# RENAME `tip_amount` AS A LABEL
val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
OneHotTRAINLabeled.cache()
OneHotTESTLabeled.cache()

# DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

# DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
val trainPct = 0.75
val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = trainValidationSplit.fit(OneHotTRAINLabeled)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

println("Test R-sqr is: " + Test_R2);
```

**Uitvoer**

Test R-Sqr is: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimaliseer het binaire classificatie model door Kruis validatie te gebruiken en Hyper-para meters te verruimen
In deze sectie wordt beschreven hoe u een binair classificatie model optimaliseert door Kruis validatie te gebruiken en Hyper-para meters te verruimen. Hierbij wordt gebruikgemaakt van de Spark ML- `CrossValidator` functie.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
indexedTRAINwithCatFeatBinTargetRF.cache()
indexedTESTwithCatFeatBinTargetRF.cache()

# DEFINE THE ESTIMATOR FUNCTION
val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

# SPECIFY THE NUMBER OF FOLDS
val numFolds = 3

# DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

# COMPUTE THE TEST F1 SCORE
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Uitvoer**

Tijd voor het uitvoeren van de cel: 33 seconden.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Het lineaire regressie model optimaliseren door gebruik te maken van aangepaste cross-validatie en code voor het opruimen van para meters
Optimaliseer vervolgens het model met behulp van aangepaste code en Identificeer de beste model parameters door het criterium van de hoogste nauw keurigheid te gebruiken. Vervolgens maakt u het laatste model, evalueert u het model op test gegevens en slaat u het model op in Blob Storage. Ten slotte laadt u het model, de test gegevens en evalueren nauw keurigheid.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

val nFolds = 3
val numModels = paramGrid.size
val numParamsinGrid = 2

# SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

var maxDepth = -1
var numTrees = -1
var param = ""
var paramval = -1
var validateLB = -1.0
var validateUB = -1.0
val h = 1.0 / nFolds;
val RMSE  = Array.fill(numModels)(0.0)

# CREATE K-FOLDS
val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
for (i <- 0 to (nFolds-1)) {
    validateLB = i * h
    validateUB = (i + 1) * h
    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    validationLabPt.cache()
    trainCVLabPt.cache()

    for (nParamSets <- 0 to (numModels-1)) {
        for (nParams <- 0 to (numParamsinGrid-1)) {
            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
            if (param == "maxDepth") {maxDepth = paramval}
            if (param == "numTrees") {numTrees = paramval}
        }
        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=numTrees, maxDepth=maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
        val labelAndPreds = validationLabPt.map { point =>
                                                 val prediction = rfModel.predict(point.features)
                                                  ( prediction, point.label )
                                                }
        val validMetrics = new RegressionMetrics(labelAndPreds)
        val rmse = validMetrics.rootMeanSquaredError
        RMSE(nParamSets) += rmse
    }
    validationLabPt.unpersist();
    trainCVLabPt.unpersist();
}
val minRMSEindex = RMSE.indexOf(RMSE.min)

# GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
var best_maxDepth = -1
var best_numTrees = -1
for (nParams <- 0 to (numParamsinGrid-1)) {
    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
    if (param == "maxDepth") {best_maxDepth = paramval}
    if (param == "numTrees") {best_numTrees = paramval}
}

# CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)

# SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "BestCV_RF_Regression__"
val filename = modelDir.concat(modelName).concat(datestamp)
best_rfModel.save(sc, filename);

# PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = best_rfModel.predict(point.features)
                                        ( prediction, point.label )
                                       }

val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");


# LOAD THE MODEL
val savedRFModel = RandomForestModel.load(sc, filename)

val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = savedRFModel.predict(point.features)
                                        ( prediction, point.label )
                                       }
# TEST THE MODEL
val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2
```

**Uitvoer**

Tijd voor het uitvoeren van de cel: 61 seconden.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Automatisch met Spark gebouwde machine learning modellen gebruiken met scala
Zie [team data Science process](./index.yml)voor een overzicht van de onderwerpen die u helpen bij het uitvoeren van de taken die het data Science-proces in azure vormen.

Met de [scenario's voor team data Science proces](walkthroughs.md) worden andere end-to-end-procedures beschreven die de stappen in het proces voor de team data Science voor specifieke scenario's illustreren. De procedures illustreren ook hoe u Cloud-en on-premises hulpprogram ma's en services in een werk stroom of pijp lijn kunt combi neren om een intelligente toepassing te maken.

Met een [Score van Spark-machine learning modellen](spark-model-consumption.md) wordt aangegeven hoe u scala code kunt gebruiken om automatisch nieuwe gegevens sets te laden en te scoren met machine learning modellen die zijn ingebouwd in Spark en opgeslagen in Azure Blob Storage. U kunt de instructies hieronder volgen en de python-code vervangen door scala-code in dit artikel voor automatisch verbruik.