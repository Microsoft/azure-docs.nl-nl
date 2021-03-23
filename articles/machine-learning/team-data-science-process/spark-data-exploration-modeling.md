---
title: Gegevens verkennen en modelleren met Spark - Team Data Science Process
description: Dit artikel geeft een overzicht van de mogelijkheden voor het verkennen en modelleren van gegevens met de Spark MLlib-toolkit in HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 531e5cf274d40eae19075514d50aec5a6524aeec
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775337"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Met Spark gegevens verkennen en modelleren

Lees hier hoe u HDInsight Spark kunt gebruiken voor het trainen van machine learning-modellen om de prijs te voorspellen van taxiritten met behulp van Spark MLlib.

In dit voorbeeld worden de verschillende stappen in het [Team Data Science Process](./index.yml) gedemonstreerd. Er wordt een subset gebruikt van de gegevensset met prijzen voor taxiritten in New York van 2013 om gegevens te laden, te verkennen en voor te bereiden. Vervolgens worden met behulp van Spark MLlib binaire classificatie- en regressiemodellen getraind om te voorspellen of er een fooi zal worden betaald voor de rit en om de hoogte van de fooi te schatten.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-account en een HDInsight-cluster met Spark 1.6 (of Spark 2.0) nodig om deze procedure te voltooien. Raadpleeg [Overview of data science using Spark on Azure HDInsight](spark-overview.md) (Overzicht van gegevenswetenschap met Spark in Azure HDInsight) voor instructies om te voldoen aan deze vereisten. Dat onderwerp bevat ook een beschrijving van de taxigegevens over 2013 voor New York die hier worden gebruikt en instructies voor het uitvoeren van code vanuit een Jupyter-notebook in het Spark-cluster. 

### <a name="spark-clusters-and-notebooks"></a>Spark-clusters en -notebooks

In dit overzicht vindt u instructies en code voor gebruik van een HDInsight Spark 1.6-cluster. Er zijn echter ook Jupyter-notebooks voor zowel HDInsight Spark 1.6- als Spark 2.0-clusters. Ga naar de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub-opslagplaats met de notebooks voor een beschrijving van de notebooks en koppelingen ernaartoe. De code in dit overzicht en in de gekoppelde notebooks is bovendien generiek en moet op ieder Spark-cluster werken. Als u geen gebruikmaakt van HDInsight Spark, kunnen de stappen voor het instellen en beheren van het cluster enigszins afwijken van wat hier wordt beschreven. Hier volgen de koppelingen naar de Jupyter-notebooks voor Spark 1.6 (uitvoeren in de pySpark-kernel van de Jupyter Notebook-server) en Spark 2.0 (uitvoeren in de pySpark3-kernel van de Jupyter Notebook-server):

- [Spark 1.6-notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/): bevatten informatie over het verkennen, modelleren en scoren van gegevens met behulp van verschillende algoritmen.
- [Spark 2.0 notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/): bevatten informatie over het uitvoeren van regressie- en classificatietaken. Gegevenssets kunnen verschillen, maar de stappen en concepten zijn van toepassing op uiteenlopende gegevenssets.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> De onderstaande beschrijvingen gelden voor het gebruik van Spark 1.6. Voor Spark 2.0-versies gebruikt u de hierboven beschreven en gekoppelde notebooks. 

## <a name="setup"></a>Instellen

Spark kan lezen van en schrijven naar Azure Storage Blob (ook wel bekend als WASB). Bestaande gegevens die daar zijn opgeslagen, kunnen dus worden verwerkt met behulp van Spark en de resultaten kunnen opnieuw worden opgeslagen in WASB.

Om modellen of bestanden op te slaan in WASB, moet het pad correct worden opgegeven. Als u wilt verwijzen naar de standaardcontainer die aan het Spark-cluster is gekoppeld, gebruikt u een pad dat begint met: 'wasb:///'. Er kan naar andere locaties worden verwezen met 'wasb://'.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Mappaden instellen voor opslaglocaties in WASB

In het volgende codevoorbeeld wordt de locatie opgegeven van de gegevens die moeten worden gelezen, evenals het pad voor de map voor modelopslag waarin de modeluitvoer wordt opgeslagen:

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
```

### <a name="import-libraries"></a>Bibliotheken importeren

De configuratie omvat ook het importeren van de benodigde bibliotheken. Gebruik de volgende code om de Spark-context in te stellen en de benodigde bibliotheken te importeren:

```python
# IMPORT LIBRARIES
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

De PySpark-kernels die worden meegeleverd met Jupyter-notebooks hebben een vooraf ingestelde context. Het is dus niet nodig om de Spark- of Hive-contexten expliciet in te stellen voordat u aan de slag gaat met de toepassing die u ontwikkelt. Deze contexten zijn standaard voor u beschikbaar. Het betreft deze contexten:

* sc - voor Spark 
* sqlContext - voor Hive

De PySpark-kernel biedt enkele vooraf gedefinieerde 'magics'. Dit zijn speciale opdrachten die u kunt aanroepen met behulp van %%. Er zijn twee van dergelijke opdrachten die worden gebruikt in deze codevoorbeelden.

* **%%local** Geeft aan dat de code op volgende regels lokaal moet worden uitgevoerd. Code moet geldige Python-code zijn.
* **%%sql -o \<variable name>** Voert een Hive-query uit op de sqlContext. Als de parameter -o wordt doorgegeven, wordt het resultaat van de query als een Panda-dataframe persistent gemaakt in de Python-context %%local.

Zie [Kernels for Jupyter notebook on Apache Spark clusters in Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md) (Kernels voor Jupyter-notebooks in Apache Spark-clusters in Azure HDInsight) voor meer informatie over Jupyter-notebook-kernels en de vooraf gedefinieerde 'magics'.

## <a name="load-the-data"></a>De gegevens laden

De eerste stap in het Data Science-proces is het opnemen van de gegevens die moeten worden geanalyseerd uit bronnen in uw omgeving voor het verkennen en modelleren van gegevens. De omgeving is Spark in dit overzicht. Deze sectie bevat de code voor het voltooien van een reeks taken:

* het gegevens-sample opnemen dat moet worden gemodelleerd
* de invoer-gegevensset lezen (opgeslagen als een TSV-bestand)
* de gegevens opmaken en opschonen
* objecten (RDD's of dataframes) maken en in het cachegeheugen opslaan
* registreren als een tijdelijke tabel (temp-table) in SQL-context

Dit is de code voor gegevensopname.

```python
# INGEST DATA

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
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

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 51,72 seconden

## <a name="explore-the-data"></a>De gegevens verkennen

Zodra de gegevens zijn overgebracht naar Spark, is de volgende stap in het datascience-proces het verkrijgen van een dieper inzicht van de gegevens door deze te verkennen en visualiseren. In deze sectie onderzoeken we de taxigegevens met behulp van SQL-query's en plotten we de doelvariabelen en potentiële functies voor visuele inspectie. We gaan met name de frequentie van het aantal passagiers in taxiritten plotten, de frequentie van fooien en hoe fooien variëren per bedrag en type betaling.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Een histogram plotten van aantal passagiers in het sample met taxiritten

Deze code en volgende fragmenten gebruiken SQL-magic om een query op het sample uit te voeren en local-magic om de gegevens te plotten.

* **SQL-magic (`%%sql`)** De HDInsight PySpark-kernel ondersteunt eenvoudige inline HiveQL-query's in de sqlContext. Het argument (-o NAAM_VARIABELE) zorgt ervoor dat de uitvoer van de SQL-query als een Panda-dataframe persistent wordt gemaakt op de Jupyter-server. Met deze instelling komt de uitvoer beschikbaar in de lokale modus.
* De **magic** `%%local` wordt gebruikt om code lokaal uit te voeren op de Jupyter-server, die het hoofdknooppunt van het HDInsight-cluster vormt. Meestal gebruikt u de magic `%%local` in combinatie met de magic `%%sql` met de parameter -o. Met de parameter -o wordt de uitvoer van de SQL-query lokaal persistent gemaakt, waarna met de %%local-magic de volgende set met codefragmenten wordt geactiveerd voor lokale uitvoering op de uitvoer van de SQL-query's die lokaal persistent zijn.

De uitvoer wordt automatisch gevisualiseerd nadat u de code hebt uitgevoerd.

Met deze query worden de ritten opgehaald op basis van het aantal passagiers. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts 
FROM taxi_train 
WHERE passenger_count > 0 and passenger_count < 7 
GROUP BY passenger_count 
```

Deze code maakt een lokaal dataframe van de queryuitvoer en plot de gegevens. Met de magic `%%local` wordt een lokaal dataframe gemaakt, `sqlResults`, dat kan worden gebruikt voor plotten met matplotlib. 

> [!NOTE]
> Deze PySpark-magic wordt meerdere keren gebruikt in dit overzicht. Als de hoeveelheid gegevens groot is, kunt u het beste een sample nemen om een dataframe te maken dat in het lokale geheugen past.

```python
#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Dit is de code voor het plotten van de ritten op aantallen passagiers:

```python
# PLOT PASSENGER NUMBER VS. TRIP COUNTS
%%local
import matplotlib.pyplot as plt
%matplotlib inline

x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**UITVOER:**

![Ritten uitgesplitst naar aantal passagiers](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

U kunt kiezen uit verschillende soorten visualisaties (tabel, cirkel, lijn, vlak of staaf) met behulp van de **Type**-knoppen in het notebook. Hier ziet u een staafdiagram.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Een histogram plotten van fooien en hoe fooien verschillen per aantal passagiers en ritbedragen

Gebruik een SQL-query om een sample van de gegevens te nemen.

```sql
# PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped 
FROM taxi_train 
WHERE passenger_count > 0 
AND passenger_count < 7 
AND fare_amount > 0 
AND fare_amount < 200 
AND payment_type in ('CSH', 'CRD') 
AND tip_amount > 0 
AND tip_amount < 25
```

In deze codecel wordt de SQL-query gebruikt om drie plots van de gegevens te maken.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 100, -2, 20])
plt.show()
```

**UITVOER:** 

![Verdeling van fooien](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Hoogte van fooi per aantal passagiers](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Hoogte van fooi per ritbedrag](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>De gegevens voorbereiden

In deze sectie wordt de code beschreven voor procedures die worden gebruikt om gegevens voor te bereiden voor gebruik in ML-modellering. Er wordt uitgelegd hoe u de volgende taken uitvoert:

* Een nieuwe functie maken door tijdstippen van ophalen (pickup_hour) toe te wijzen aan tariefperioden (buckets)
* Categorische-functies indexeren en coderen
* Gelabelde puntobjecten maken voor invoer in ML-functies
* Een willekeurige subsampling van de gegevens maken en deze splitsen in trainings- en testsets
* Functie schalen
* Objecten opslaan in cachegeheugen

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Een nieuwe functie maken door tijdstippen van ophalen toe te wijzen aan tariefperioden

Deze code laat zien hoe u een nieuwe functie maakt door tijdstippen van ophalen toe te wijzen aan tariefperioden en vervolgens het resulterende dataframe op te slaan in het cachegeheugen. In situaties waarin RDD's (Resilient Distributed Datasets) en dataframes herhaaldelijk worden gebruikt, resulteert caching in kortere uitvoeringstijden. Daarom slaan we RDD's en dataframes tijdens het overzicht regelmatig op. 

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**UITVOER:**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Categorische functies indexeren en coderen voor invoer in modelleerfuncties

In deze sectie wordt beschreven hoe u categorische functies indexeert of codeert voor invoer in de modelleerfuncties. De functies van MLlib voor modelleren en voorspellen, vereisen dat functies met categorische invoergegevens worden geïndexeerd of gecodeerd voordat ze worden gebruikt. Afhankelijk van het model moet u de functie op verschillende manieren indexeren of coderen:  

* **Structuurmodellen** vereisen dat categorieën worden gecodeerd als numerieke waarden (een functie met drie categorieën kan bijvoorbeeld worden gecodeerd met 0, 1, 2). Dit algoritme wordt aangeboden door de functie [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) van MLlib. Deze functie codeert een tekenreekskolom van labels als een kolom met labelindexen die worden geordend op labelfrequentie. Hoewel ze worden geïndexeerd met numerieke waarden voor invoer en gegevensverwerking, kunnen de structuuralgoritmen worden opgegeven om ze te behandelen als categorieën. 
* **Logistieke en lineaire regressiemodellen** vereisen one-hot codering, waarbij, bijvoorbeeld, een functie met drie categorieën kan worden uitgebreid naar drie functiekolommen, met elk een waarde van 0 of 1, afhankelijk van de categorie van een waarneming. MLlib biedt de functie [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) voor het uitvoeren van one-hot codering. Met dit coderingsprogramma wordt een kolom met labelindexen toegewezen aan een kolom met binaire vectoren, met maximaal één een-waarde. Met deze codering kunnen algoritmen die functies met numerieke waarden verwachten, zoals logistieke regressie, worden toegepast op categorische-functies.

Dit is de code voor het indexeren en coderen van categorische-functies:

```python
# INDEX AND ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES    
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
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

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 1,28 seconden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gelabelde puntobjecten maken voor invoer in ML-functies

Deze sectie bevat code die laat zien hoe u categorische tekstgegevens indexeert als gelabelde punten en hoe u deze kunt coderen zodat ze kunnen worden gebruikt voor het trainen en testen van MLlib-modellen voor logistieke regressie en andere classificaties. Gelabelde puntobjecten zijn RDD's die zijn opgemaakt als invoergegevens voor de meeste ML-algoritmen in MLlib. Een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, hetzij dense of sparse, die is gekoppeld aan een label/respons.  

Deze sectie bevat code die laat zien hoe u categorische tekstgegevens indexeert als [gelabelde punten](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) en hoe u deze kunt coderen zodat ze kunnen worden gebruikt voor het trainen en testen van MLlib-modellen voor logistieke regressie en andere classificaties. Gelabelde puntobjecten zijn RDD's die bestaan uit een label (doel-/responsvariabele) en een functievector. Deze indeling is vereist als invoer voor een groot aantal ML-algoritmen in MLlib.

Hier volgt de code voor het indexeren en coderen van tekstfuncties voor binaire classificatie.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

Hier volgt de code voor het coderen en indexeren van categorische-tekstfuncties voor lineaire regressieanalyse.

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])

    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Een willekeurige subsampling van de gegevens maken en deze splitsen in trainings- en testsets

Met deze code neemt u een willekeurig sample van de gegevens (25% wordt hier gebruikt). Hoewel dit niet vereist is voor dit voorbeeld vanwege de grootte van de gegevensset, laten we zien hoe u hier een sample neemt zodat u weet hoe u dit voor uw eigen probleem kunt gebruiken wanneer dat nodig is. Wanneer samples groot zijn, kan sampling aanzienlijk tijd besparen bij het trainen van modellen. Vervolgens splitsen we het sample in een trainingsdeel (75% hier) en een testdeel (25% hier) voor gebruik in de classificatie- en regressiemodellen.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.sql.functions import rand

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 0,24 seconde

### <a name="feature-scaling"></a>Functie schalen

Het schalen van functies, ook wel bekend als gegevensnormalisatie, zorgt ervoor dat functies met weid verspreide waarden geen buitensporige weging krijgen toegewezen in de doelfunctie. De code voor functieschaling maakt gebruik van de [StandardScaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.classification.LogisticRegressionWithLBFGS.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS
) om de functies te schalen op eenheidvariantie. StandardScaler wordt verstrekt door MLlib voor gebruik in lineaire regressie met SGD (Stochastic Gradient Descent), een populair algoritme voor het trainen van een breed scala aan andere machine learning-modellen, zoals geregulariseerde regressies of SVM's (Support Vector Machines).

> [!NOTE]
> We hebben vastgesteld dat het algoritme LinearRegressionWithSGD gevoelig is functieschaling.

Hier volgt de code voor het schalen van variabelen voor gebruik met het geregulariseerde lineaire SGD-algoritme.

```python
# FEATURE SCALING

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 13,17 seconden

### <a name="cache-objects-in-memory"></a>Objecten opslaan in cachegeheugen

De tijd die nodig is voor het trainen en testen van ML-algoritmen kan worden beperkt door de invoergegevensframe-objecten die worden gebruikt voor classificatie, regressie en geschaalde functies in de cache op te slaan.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:** 

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 0,15 seconde

## <a name="train-a-binary-classification-model"></a>Een model voor binaire classificatie trainen

In deze sectie wordt beschreven hoe u drie modellen gebruikt om via binaire classificatie te voorspellen of er al dan niet een fooi wordt gegeven voor een taxirit. Het betreft deze modellen:

* Geregulariseerde logistieke regressie 
* Random forest-model
* Gradient Boosting Trees

Elke sectie voor het ontwikkelen van een model is onderverdeeld in stappen: 

1. **Modelgegevens trainen** met één parameterset
2. **Modelevaluatie** met een testgegevensset met metrische gegevens
3. **Model opslaan** in blob voor toekomstig gebruik

### <a name="classification-using-logistic-regression"></a>Classificatie met behulp van logistiek regressie

De code in deze sectie laat zien hoe u een logistiek regressiemodel traint, evalueert en opslaat met [BFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm), een algoritme dat voorspelt of er al dan niet een fooi wordt gegeven voor een taxirit in de gegevensset over taxigebruik in New York.

**Het logistieke regressiemodel trainen met CV en sweeping van hyperparameters**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics


# CREATE MODEL WITH ONE SET OF PARAMETERS
logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                               tolerance=0.0001, validateData=True, numClasses=2)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitModel.weights))
print("Intercept: " + str(logitModel.intercept))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:** 

Coëfficiënten: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept: -0.0111216486893

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 14,43 seconden

**Het binaire classificatiemodel evalueren met standaard metrische gegevens**

```python
#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

# RECORD START TIME
timestart = datetime.datetime.now()

# PREDICT ON TEST DATA WITH MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)


## SAVE MODEL WITH DATE-STAMP
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;
logitModel.save(sc, dirfilename);

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UITVOER:** 

Area under precision = 0.985297691373

Area under ROC = 0.983714670256

Overall Statistics

Precision = 0.984304060189

Recall = 0.984304060189

F1 Score = 0.984304060189

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 57,61 seconden

**De ROC-curve plotten**

*predictionAndLabelsDF* wordt geregistreerd als een tabel, *tmp_results*, in de vorige cel. *tmp_results* kan worden gebruikt om query's uit te voeren en resultaten uit te voeren in het dataframe sqlResults voor plotten. Dit is de code.

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Hier volgt de code voor het maken van voorspellingen en het plotten van de ROC-curve.

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

# MAKE PREDICTIONS
predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVE
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

**UITVOER:**

![Logistic regression ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Random forest-classificatie

De code in deze sectie laat zien hoe u een random forest-model traint, evalueert en opslaat met BFGS, een algoritme dat voorspelt of er al dan niet een fooi wordt gegeven voor een taxirit in de gegevensset over taxigebruik in New York.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRINT TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Area under ROC = 0.985297691373

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 31,09 seconden

### <a name="gradient-boosting-trees-classification"></a>Gradient Boosting Trees-classificatie

De code in deze sectie laat zien hoe u een Gradient Boosting Trees-model traint, evalueert en opslaat met BFGS, een algoritme dat voorspelt of er al dan niet een fooi wordt gegeven voor een taxirit in de gegevensset over taxigebruik in New York.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Area under ROC = 0.985297691373

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 19,76 seconden

## <a name="train-a-regression-model"></a>Een regressiemodel trainen

In deze sectie wordt uitgelegd hoe u drie modellen gebruikt om via regressie te voorspellen hoeveel fooi er wordt gegeven voor een taxirit op basis van andere fooifuncties. Het betreft deze modellen:

* Gereguleerde lineaire regressie
* Random forest
* Gradient Boosting Trees

Deze modellen zijn beschreven in de inleiding. Elke sectie voor het ontwikkelen van een model is onderverdeeld in stappen: 

1. **Modelgegevens trainen** met één parameterset
2. **Modelevaluatie** met een testgegevensset met metrische gegevens
3. **Model opslaan** in blob voor toekomstig gebruik

### <a name="linear-regression-with-sgd"></a>Lineaire regressie met SGD

De code in deze sectie laat zien hoe u geschaalde functies gebruikt om een lineaire regressie te trainen die gebruikmaakt van SGD (Stochastic Gradient Descent) voor optimalisatie, en hoe u het model kunt beoordelen, evalueren en opslaan in Azure Blob Storage (WASB).

> [!TIP]
> In onze ervaring kunnen er problemen optreden met de convergentie van LinearRegressionWithSGD-modellen en moeten parameters zorgvuldig worden gewijzigd/geoptimaliseerd voor het verkrijgen van een geldig model. Het schalen van variabelen is zeer nuttig voor convergentie.

```python
#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

# PRINT TEST METRICS
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Coëfficiënten: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Intercept: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 58,42 seconden

### <a name="random-forest-regression"></a>Random Forest-regressie

De code in deze sectie laat zien hoe u een model voor Random Forest-regressie kunt trainen, evalueren en opslaan waarmee de fooien worden voorspeld voor taxiritten in New York.

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

## PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

RMSE = 0.891209218139

R-sqr = 0.759661334921

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 49,21 seconden

### <a name="gradient-boosting-trees-regression"></a>Gradient Boosting Trees-regressie

De code in deze sectie laat zien hoe u een Gradient Boosting Trees-model kunt trainen, evalueren en opslaan waarmee de fooien worden voorspeld voor taxiritten in New York.

**Trainen en evalueren**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

## EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
test_predictions = sqlContext.createDataFrame(predictionAndLabels)
test_predictions.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

RMSE = 0.908473148639

R-sqr = 0.753835096681

Benodigde tijd voor het uitvoeren van de bovenstaande cel: 34,52 seconden

**Plotten**

*tmp_results* wordt geregistreerd als een Hive-tabel in de vorige cel. Resultaten uit de tabel worden uitgevoerd in het dataframe *sqlResults* om te worden geplot. Dit is de code:

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Dit is de code voor het plotten van de gegevens met behulp van de Jupyter-server.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
import numpy as np

# PLOT 
ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
plt.axis([-1, 20, -1, 20])
plt.show(ax)
```

**UITVOER:**

![Werkelijke versus voorspelde fooien](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Objecten uit het geheugen opschonen

Gebruik `unpersist()` om objecten te verwijderen die in het cachegeheugen zijn opgeslagen.

```python
# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

## <a name="save-the-models"></a>De modellen opslaan

Als u een onafhankelijke gegevensset wilt gebruiken en beoordelen die wordt beschreven in het onderwerp [Operationalize Spark-built machine learning models](spark-model-consumption.md) (In Spark gebouwde machine learning-modellen operationeel maken), moet u deze bestandsnamen met de opgeslagen modellen die u hier hebt gemaakt, kopiëren en plakken in het Jupyter-notebook Consumption. Dit is de code voor het weergeven van de paden naar modelbestanden die u daar nodig hebt.

```python
# MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**UITVOER:**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Volgende stappen

U hebt nu regressie- en classificatiemodellen gemaakt met de Spark MlLib en bent dus klaar om te leren hoe u deze modellen kunt beoordelen en evalueren. Het notebook voor geavanceerde gegevensverkenning en -modellering gaat verder de diepte in, met aandacht voor kruisvalidatie, sweeping van hyperparameters en modelevaluatie. 

**Modelverbruik:** Voor meer informatie over het beoordelen en evalueren van de classificatie- en regressiemodellen die in dit onderwerp zijn gemaakt, raadpleegt u [Operationalize Spark-built machine learning models](spark-model-consumption.md) (In Spark gebouwde machine learning-modellen operationeel maken).

**Kruisvalidatie en sweeping van hyperparameters**: Zie [Advanced data exploration and modeling with Spark](spark-advanced-data-exploration-modeling.md) (Geavanceerde gegevensverkenning en -modellering met Spark) over de manier waarop modellen kunnen worden getraind met behulp van kruisvalidatie en sweeping van hyperparameters.
