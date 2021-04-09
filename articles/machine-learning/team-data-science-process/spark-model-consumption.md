---
title: 'Met operationeel maken Spark ontwikkelde machine learning modellen: team data Science process'
description: Het laden en beoordelen van leer modellen die zijn opgeslagen in Azure Blob Storage (WASB) met python.
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
ms.openlocfilehash: 659f61734001163ada6fd26caabe2fb007892f61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774487"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Met operationeel maken Spark ontwikkelde machine learning modellen

In dit onderwerp wordt beschreven hoe u een opgeslagen machine learning model (ML) operationeel maken met behulp van python in HDInsight Spark-clusters. Hierin wordt beschreven hoe u machine learning modellen laadt die zijn gebouwd met Spark MLlib en zijn opgeslagen in Azure Blob Storage (WASB) en hoe u deze kunt beoordelen met gegevens sets die ook zijn opgeslagen in WASB. U ziet hoe u de invoer gegevens vooraf kunt verwerken, functies kunt transformeren met behulp van de functies voor indexeren en coderen in de MLlib Toolkit, en hoe u een object met een label punt gegevens maakt dat kan worden gebruikt als invoer voor het scoren met de ML-modellen. De modellen die worden gebruikt voor Score zijn onder andere lineaire regressie, logistiek regressie, wille keurige forest-modellen en verlopen boom modellen.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark-clusters en Jupyter-notebooks
De installatie stappen en de code voor het operationeel maken van een ML-model zijn opgenomen in dit overzicht voor het gebruik van een HDInsight Spark 1,6-cluster en een Spark 2,0-cluster. De code voor deze procedures is ook opgenomen in Jupyter-notebooks.

### <a name="notebook-for-spark-16"></a>Notebook voor Spark 1,6
De [pySpark-machine learning-data-Science-Spark-ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook laat zien hoe u een opgeslagen model kunt operationeel maken met behulp van python op HDInsight-clusters. 

### <a name="notebook-for-spark-20"></a>Notebook voor Spark 2,0
Als u de Jupyter-notebook voor Spark 1,6 wilt wijzigen voor gebruik met een HDInsight Spark 2,0-cluster, vervangt u het python-code bestand door [dit bestand](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Deze code laat zien hoe u de modellen die zijn gemaakt in Spark 2,0 verbruikt.


## <a name="prerequisites"></a>Vereisten

1. U hebt een Azure-account en een HDInsight-cluster met Spark 1.6 (of Spark 2.0) nodig om deze procedure te voltooien. Raadpleeg [Overview of data science using Spark on Azure HDInsight](spark-overview.md) (Overzicht van gegevenswetenschap met Spark in Azure HDInsight) voor instructies om te voldoen aan deze vereisten. Dat onderwerp bevat ook een beschrijving van de taxigegevens over 2013 voor New York die hier worden gebruikt en instructies voor het uitvoeren van code vanuit een Jupyter-notebook in het Spark-cluster. 
2. Maak de machine learning modellen die u hier kunt beoordeelt door de [gegevens te verkennen en te model leren met het Spark](spark-data-exploration-modeling.md) -onderwerp voor het Spark 1,6-cluster of de Spark 2,0-notebooks. 
3. De Spark 2,0-notebooks gebruiken een extra gegevensset voor de classificatie taak, de bekende luchtvaart maatschappij op tijd van 2011 en 2012. Ga naar de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub-opslagplaats met de notebooks voor een beschrijving van de notebooks en koppelingen ernaartoe. De code in dit overzicht en in de gekoppelde notebooks is bovendien generiek en moet op ieder Spark-cluster werken. Als u geen gebruikmaakt van HDInsight Spark, kunnen de stappen voor het instellen en beheren van het cluster enigszins afwijken van wat hier wordt beschreven. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Setup: opslag locaties, Bibliotheken en de vooraf ingestelde Spark-context
Spark kan lezen van en schrijven naar een Azure Storage Blob (WASB). Bestaande gegevens die daar zijn opgeslagen, kunnen dus worden verwerkt met behulp van Spark en de resultaten kunnen opnieuw worden opgeslagen in WASB.

Om modellen of bestanden op te slaan in WASB, moet het pad correct worden opgegeven. Er kan naar de standaard container die aan het Spark-cluster is gekoppeld, een pad worden gebruikt dat begint met: *' wasb///'*. In het volgende code voorbeeld geeft u de locatie op van de gegevens die moeten worden gelezen en het pad voor de map voor het model opslag waarnaar de model uitvoer wordt opgeslagen. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Mappaden instellen voor opslaglocaties in WASB
Modellen worden opgeslagen in: "wasb:///user/remoteuser/NYCTaxi/Models". Als dit pad niet juist is ingesteld, worden modellen niet geladen voor scores.

De gescoorde resultaten zijn opgeslagen in: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Als het pad naar de map onjuist is, worden de resultaten niet opgeslagen in die map.   

> [!NOTE]
> De locatie van het bestandspad kan worden gekopieerd en in de tijdelijke aanduidingen in deze code worden geplakt vanuit de uitvoer van de laatste cel van de **machine learning-data-Science-Spark-data-Explore-Modeling. ipynb** notebook.   
> 
> 

Hier volgt de code voor het instellen van mappaden: 

```python
# LOCATION OF DATA TO BE SCORED (TEST DATA)
taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

# SET SCORDED RESULT DIRECTORY PATH
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

# FILE LOCATIONS FOR THE MODELS TO BE SCORED
logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

# RECORD START TIME
import datetime
datetime.datetime.now()
```

**UITVOER:**

DateTime. datetime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Bibliotheken importeren
Stel Spark-context in en importeer de benodigde bibliotheken met de volgende code

```python
#IMPORT LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Vooraf ingestelde Spark-context en PySpark-magics
De PySpark-kernels die worden meegeleverd met Jupyter-notebooks hebben een vooraf ingestelde context. Daarom hoeft u de Spark-of Hive-contexten expliciet niet in te stellen voordat u begint te werken met de toepassing die u ontwikkelt. Deze contexten zijn standaard beschikbaar:

* sc - voor Spark 
* sqlContext - voor Hive

De PySpark-kernel biedt enkele vooraf gedefinieerde 'magics'. Dit zijn speciale opdrachten die u kunt aanroepen met behulp van %%. Er zijn twee van dergelijke opdrachten die worden gebruikt in deze codevoorbeelden.

* **%% Local** Opgegeven dat de code in volgende regels lokaal wordt uitgevoerd. Code moet geldige Python-code zijn.
* **%% SQL-o \<variable name>** 
* Voert een Hive-query uit op basis van de sqlContext. Als de-o-para meter wordt door gegeven, wordt het resultaat van de query persistent gemaakt in de lokale python-context%% als een Panda data frame.

Zie [kernels die beschikbaar zijn voor Jupyter-notebooks met Hdinsight Spark Linux-clusters in hdinsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)voor meer informatie over de kernels voor Jupyter-notebooks en de vooraf gedefinieerde ' magics ' die ze bieden.

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Gegevens opnemen en een gereinigd gegevens frame maken
Deze sectie bevat de code voor een reeks taken die nodig zijn voor het opnemen van de gegevens die moeten worden beoordeeld. Lees in een gecombineerd 0,1%-voor beeld van de taxi en het ritbedrag bestand (opgeslagen als een TSV-bestand) de gegevens op en maak vervolgens een schoon gegevens frame.

De taxi-en ritbedrag bestanden zijn gekoppeld aan de hand van de procedure in het [team data Science process in actie: het onderwerp HDInsight Hadoop-clusters gebruiken](hive-walkthrough.md) .

```python
# INGEST DATA AND CREATE A CLEANED DATA FRAME

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_test_file = sc.textFile(taxi_test_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_test_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# CREATE DATA FRAME
taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_cleaned.cache()
taxi_df_test_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_test_cleaned.registerTempTable("taxi_test")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 46,37 seconden

## <a name="prepare-data-for-scoring-in-spark"></a>Gegevens voorbereiden voor score in Spark
In deze sectie wordt beschreven hoe u categorische-functies indexeert, versleutelt en schaalt om ze voor te bereiden voor gebruik in MLlib bewaakte leer algoritmen voor classificatie en regressie.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Functie transformatie: categorische-functies indexeren en coderen voor invoer in modellen voor het scoren
In deze sectie wordt uitgelegd hoe u categorische gegevens indexeert met behulp `StringIndexer` van de functies en code ring met `OneHotEncoder` invoer in de modellen.

De [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) codeert een teken reeks kolom van labels naar een kolom met label indexen. De indexen worden gesorteerd op label frequenties. 

De [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) wijst een kolom met label indexen toe aan een kolom met binaire vectoren, met Maxi maal één waarde. Met deze code ring kunt u algoritmen verwachten van functies met continue waarden, zoals logistiek regressie, die worden toegepast op categorische-functies.

```python
#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_test 
"""
taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_with_newFeatures.cache()
taxi_df_test_with_newFeatures.count()

# INDEX AND ONE-HOT ENCODING
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_test_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND ENCODE TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 5,37 seconden

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>RDD-objecten maken met functie matrices voor invoer in modellen
Deze sectie bevat code die laat zien hoe u categorische tekst gegevens indexeert als een RDD-object en een-hot-code ring zodat deze kan worden gebruikt voor het trainen en testen van MLlib logistiek-regressie en structuur modellen. De geïndexeerde gegevens worden opgeslagen in [rdd-objecten (robuuste gedistribueerde gegevensset)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . De Rdd's zijn de basis abstractie in Spark. Een RDD-object vertegenwoordigt een onveranderbare, gepartitioneerde verzameling elementen die parallel met Spark kan worden uitgevoerd.

Het bevat ook code die laat zien hoe u gegevens kunt schalen met de `StandardScalar` MLlib voor gebruik in lineaire regressie met stochastische Gradient Daal (SGD), een populair algoritme voor het trainen van een breed scala aan machine learning modellen. De [StandardScaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.tree.RandomForest.html#pyspark.mllib.tree.RandomForest
) wordt gebruikt om de functies te schalen op eenheids afwijking. Het schalen van functies, ook wel bekend als gegevensnormalisatie, zorgt ervoor dat functies met weid verspreide waarden geen buitensporige weging krijgen toegewezen in de doelfunctie. 

```python
# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

# CACHE RDDS IN MEMORY
indexedTESTbinary.cache();
oneHotTESTbinary.cache();
indexedTESTreg.cache();
oneHotTESTreg.cache();
oneHotTESTregScaled.cache();

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 11,72 seconden

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Score met het logistiek-regressie model en de uitvoer naar de BLOB opslaan
De code in deze sectie laat zien hoe u een logistiek regressie model laadt dat is opgeslagen in Azure Blob Storage en hoe u het kunt gebruiken om te voors pellen of een tip wordt betaald bij een taxi, een score voor de metrische gegevens van de standaard classificatie is, en de resultaten vervolgens op te slaan en uit te zetten in Blob Storage. De gescoorde resultaten worden opgeslagen in RDD-objecten. 

```python
# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

## LOAD SAVED MODEL
savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

## SAVE SCORED RESULTS (RDD) TO BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
dirfilename = scoredResultDir + logisticregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 19,22 seconden

## <a name="score-a-linear-regression-model"></a>Een lineair regressie model beoordelen
We hebben [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) gebruikt om een lineair regressie model te trainen met de stochastische Gradient DAAL (SGD) voor Optima Lise ring om de hoeveelheid fooien te voors pellen. 

De code in deze sectie laat zien hoe u een lineair regressie model laadt vanuit Azure Blob-opslag, een score met geschaalde variabelen en de resultaten vervolgens weer opslaat in de blob.

```python
#SCORE LINEAR REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

#LOAD LIBRARIES
from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

# LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = scoredResultDir + linearregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 16,63 Seconden

## <a name="score-classification-and-regression-random-forest-models"></a>Score classificatie en regressieve forest-modellen
De code in deze sectie laat zien hoe u de opgeslagen classificatie en regressieve forest-modellen laadt die zijn opgeslagen in Azure Blob Storage, hun prestaties beoordelen met standaard classificatie en regressie metingen en de resultaten vervolgens weer opslaan in Blob Storage.

[Wille keurige forests](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) zijn ensembles van beslissings structuren.  Ze combi neren veel beslissings structuren om het risico van overneming te verminderen. Wille keurige forests kunnen categorische-functies afhandelen, uitbreiden naar de classificatie-instelling voor meervoudige klassen, geen functie schaaling vereisen en niet-lineaire en functie-interacties vastleggen. Wille keurige forests zijn een van de meest succes volle machine learning modellen voor classificatie en regressie.

[Spark. mllib](https://spark.apache.org/mllib/) ondersteunt wille keurige forests voor binaire en multiklasse-classificatie en voor regressie, met behulp van doorlopende en categorische-functies. 

```python
# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES    
from pyspark.mllib.tree import RandomForest, RandomForestModel


# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 31,07 seconden

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Score classificatie en regressieve kleur overgang die structuur modellen stimuleren
De code in deze sectie laat zien hoe u de inzoomen van de classificatie en regressie kunt laten verlopen vanuit Azure Blob-opslag, de prestaties te beoordelen met standaard classificatie en regressie metingen en vervolgens de resultaten vervolgens weer in Blob Storage op te slaan. 

**Spark. mllib** ondersteunt GBTS voor binaire classificatie en voor regressie, met behulp van doorlopende en categorische-functies. 

GBTS ( [Gradient Boosting trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) zijn ensembles van beslissings structuren. GBTSer boom structuren van de trein om een verlies functie te minimaliseren. GBTS kan categorische-functies afhandelen, geen functie schaaling vereisen en niet-lineaire en functie-interacties vastleggen. Dit algoritme kan ook worden gebruikt in een instelling met een classificatie met een hoge klasse.

```python
# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

#LOAD AND SCORE THE MODEL
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + btclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

# LOAD AND SCORE MODEL 
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + btregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 14,6 seconden

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Objecten opschonen van geheugen en locaties van gescoorde bestanden afdrukken

```python
# UNPERSIST OBJECTS CACHED IN MEMORY
taxi_df_test_cleaned.unpersist()
indexedTESTbinary.unpersist();
oneHotTESTbinary.unpersist();
indexedTESTreg.unpersist();
oneHotTESTreg.unpersist();
oneHotTESTregScaled.unpersist();


# PRINT OUT PATH TO SCORED OUTPUT FILES
print "logisticRegFileLoc: " + logisticregressionfilename;
print "linearRegFileLoc: " + linearregressionfilename;
print "randomForestClassificationFileLoc: " + rfclassificationfilename;
print "randomForestRegFileLoc: " + rfregressionfilename;
print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
print "BoostedTreeRegressionFileLoc: " + btregressionfilename;
```

**UITVOER:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05 -0317 _22_ 58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Spark-modellen gebruiken via een webinterface
Spark biedt een mechanisme voor het extern indienen van batch taken of interactieve query's via een REST-interface met een onderdeel genaamd livy. Livy is standaard ingeschakeld voor uw HDInsight Spark-cluster. Zie voor meer informatie over livy: [Spark-taken extern verzenden met behulp van livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

U kunt livy gebruiken om op afstand een taak te verzenden die batch een bestand vergeeft dat is opgeslagen in een Azure-Blob en de resultaten vervolgens naar een andere blob schrijft. Hiervoor uploadt u het python-script uit  
[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) naar de blob van het Spark-cluster. U kunt een hulp programma als **Microsoft Azure Storage Explorer** of **AzCopy** gebruiken om het script naar de cluster-BLOB te kopiëren. In ons geval hebben we het script geüpload naar **_wasb:///example/python/ConsumeGBNYCReg.py_**.   

> [!NOTE]
> De toegangs sleutels die u nodig hebt, kunt u vinden in de portal voor het opslag account dat is gekoppeld aan het Spark-cluster. 
> 
> 

Nadat het script naar deze locatie is geüpload, wordt dit uitgevoerd binnen het Spark-cluster in een gedistribueerde context. Het model wordt geladen en voor spellingen worden uitgevoerd op invoer bestanden op basis van het model.  

U kunt dit script op afstand aanroepen door een eenvoudige HTTPS/REST-aanvraag op livy te maken.  Hier volgt een krul opdracht voor het maken van de HTTP-aanvraag om het python-script op afstand aan te roepen. Vervang CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME door de juiste waarden voor uw Spark-cluster.

```console
# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches
```

U kunt elke taal op het externe systeem gebruiken om de Spark-taak via livy aan te roepen door een eenvoudige HTTPS-aanroep met basis verificatie te maken.   

> [!NOTE]
> Het is handig om de bibliotheek voor python-aanvragen te gebruiken bij het maken van deze HTTP-aanroep, maar deze is momenteel niet standaard geïnstalleerd in Azure Functions. Daarom worden er in plaats daarvan oudere HTTP-bibliotheken gebruikt.   
> 
> 

Dit is de python-code voor de HTTP-aanroep:

```python
#MAKE AN HTTPS CALL ON LIVY. 

import os

# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
import httplib, urllib, base64

# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
host = '<spark cluster name>.azurehdinsight.net:443'
username='<username>'
password='<password>'

#AUTHORIZATION
conn = httplib.HTTPSConnection(host)
auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
response = conn.getresponse().read()
print(response)
conn.close()
```

U kunt deze python-code ook toevoegen aan [Azure functions](https://azure.microsoft.com/documentation/services/functions/) om een Spark-taak inzending te activeren die een BLOB verstuurt op basis van verschillende gebeurtenissen, zoals een timer, het maken of bijwerken van een blob. 

Als u de voor keur geeft aan een gratis client ervaring, gebruikt u de [Azure Logic apps](https://azure.microsoft.com/documentation/services/app-service/logic/) om de Spark batch score te roepen door een http-actie te definiëren op de **Logic apps Designer** en de para meters in te stellen. 

* Maak vanuit Azure Portal een nieuwe logische app door **+ nieuwe**  ->  **Web en mobiel**  ->  **Logic-app** te selecteren. 
* Als u de **Logic apps Designer** wilt weer geven, voert u de naam van de logische app in en app service plan.
* Selecteer een HTTP-actie en voer de para meters in die worden weer gegeven in de volgende afbeelding:

![Logic Apps Designer](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Volgende stappen
**Kruis validatie en afstemming sweep**: Zie [geavanceerde gegevens exploratie en model leren met Spark](spark-advanced-data-exploration-modeling.md) over hoe modellen kunnen worden getraind met kruis validatie en Hyper-para meter sweep.

