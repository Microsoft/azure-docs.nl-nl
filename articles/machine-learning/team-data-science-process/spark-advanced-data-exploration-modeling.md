---
title: Geavanceerde gegevens exploratie en-modellering met Spark-team data Science process
description: Gebruik HDInsight Spark om gegevens te verkennen en binaire classificatie en regressie modellen te trainen met behulp van kruis validatie en afstemming optimalisatie.
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
ms.openlocfilehash: b84b7387411e7df8e092a2e810591697319f9554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774674"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Met Spark verkennen en modelleren van geavanceerde gegevens

In deze walkthrough wordt gebruikgemaakt van HDInsight Spark om gegevens te verkennen en binaire classificatie en regressie modellen te trainen met behulp van kruis validatie en afstemming-optimalisatie op basis van een voor beeld van de NYC taxi en ritbedrag 2013-gegevensset. U wordt begeleid bij de stappen van het [Data Science proces](./index.yml), end-to-end, met behulp van een HDInsight Spark-cluster voor verwerking en Azure-blobs voor het opslaan van de gegevens en de modellen. Met het proces worden gegevens van een Azure Storage Blob verkend en gevisualiseerd. vervolgens worden de gegevens voor het ontwikkelen van voorspellende modellen voor bereid. Python is gebruikt om de oplossing te coderen en om de relevante waarnemings punten weer te geven. Deze modellen worden gebouwd met behulp van de Spark MLlib Toolkit om binaire classificatie en regressie model taken uit te voeren. 

* De **binaire classificatie** taak is om te voors pellen of een tip voor de reis wordt betaald. 
* De **regressie** taak is om de hoeveelheid fooien te voors pellen op basis van andere tip-functies. 

De stappen voor model lering bevatten ook code die laat zien hoe u elk type model traint, evalueert en opslaat. Het onderwerp behandelt een deel van hetzelfde terrein als het [verkennen van gegevens en het model leren met het Spark](spark-data-exploration-modeling.md) -onderwerp. Het is echter meer ' Geavanceerd ' in dat geval ook Kruis validatie met afstemming sweeping om optimaal nauw keurige classificatie-en regressie modellen te trainen. 

**Kruis validatie (AVK)** is een techniek waarmee wordt beoordeeld hoe goed een model dat is getraind op een bekende set gegevens, het voors pellen van de functies van gegevens sets waarop het niet is getraind.  Een algemene implementatie die hier wordt gebruikt, bestaat uit het verdelen van een gegevensset in K vouwen en vervolgens het model op een Round-Robin op alle, maar een van de vouwen. De mogelijkheid van het model om nauw keurig te worden voorspeld bij het testen op basis van de onafhankelijke gegevensset in deze vouw die niet wordt gebruikt om het model te trainen, wordt geëvalueerd.

**Afstemming Optimization** is het probleem bij het kiezen van een set Hyper parameters voor een leer algoritme, meestal met het doel om een meting van de prestaties van het algoritme op een onafhankelijke gegevensset te optimaliseren. **Hyper parameters** zijn waarden die buiten de model trainings procedure moeten worden opgegeven. Veronderstellingen over deze waarden kunnen invloed hebben op de flexibiliteit en nauw keurigheid van de modellen. Beslissings structuren hebben bijvoorbeeld Hyper parameters, zoals de gewenste diepte en het aantal bladeren in de boom structuur. Voor ondersteuning voor vector machines (SVMs) is een niet-geclassificeerde straf voorwaarde vereist. 

Een veelgebruikte manier om de afstemming-optimalisatie uit te voeren die hier wordt gebruikt, is een raster zoekactie of een **parameter sweep**. Deze zoek opdracht gaat door een subset van de afstemming ruimte voor een leer algoritme. Kruis validatie kan een prestatie metriek bieden om de optimale resultaten te sorteren die worden geproduceerd door het zoek algoritme voor rasters. CV dat met afstemming-verruiming wordt gebruikt, helpt u bij het beperken van problemen, zoals het aanpassen van een model voor het trainen van gegevens, zodat het model de capaciteit behoudt die moet worden toegepast op de algemene set gegevens waaruit de trainings gegevens zijn geëxtraheerd.

De modellen die we gebruiken zijn onder andere logistiek en lineaire regressie, wille keurige forests en verlopen structuren:

* [Lineaire regressie met SGD](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html#pyspark.mllib.regression.LinearRegressionWithSGD
) is een lineair regressie model dat gebruikmaakt van een stochastische Gradient DAAL (SGD)-methode en voor Optima Lise ring en functie schaaling om de fooien te voors pellen. 
* [Logistiek regressie met LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) of ' logit-regressie ' is een regressie model dat kan worden gebruikt wanneer de afhankelijke variabele categorische is voor het uitvoeren van de gegevens classificatie. LBFGS is een quasi-Newton-optimalisatie algoritme die de Broyden-Fletcher – Goldfarb – Shanno-algoritme (BFGS) benadert met een beperkte hoeveelheid computer geheugen en die veel wordt gebruikt in machine learning.
* [Wille keurige forests](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) zijn ensembles van beslissings structuren.  Ze combi neren veel beslissings structuren om het risico van overneming te verminderen. Wille keurige forests worden gebruikt voor regressie en classificatie en kunnen categorische-functies verwerken en kunnen worden uitgebreid naar de classificatie-instelling met een klasse. Ze hoeven niet te worden geschaald en kunnen niet-lineaire en functie-interacties worden vastgelegd. Wille keurige forests zijn een van de meest succes volle machine learning modellen voor classificatie en regressie.
* GBTS ( [Gradient, boosted trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) zijn ensembles van beslissings structuren. GBTSer boom structuren van de trein om een verlies functie te minimaliseren. GBTS wordt gebruikt voor regressie en classificatie, en kan categorische-functies afhandelen, geen functie schaaling vereisen en niet-lineaire en functie-interacties kunnen vastleggen. Ze kunnen ook worden gebruikt in een instelling met een classificatie met een hoge klasse.

Model voorbeelden die gebruikmaken van AVK en afstemming sweep worden weer gegeven voor het probleem met de binaire classificatie. Eenvoudiger voor beelden (zonder parameter sweeps) worden weer gegeven in het hoofd onderwerp voor regressie taken. Maar in de bijlage wordt validatie met behulp van elastisch net voor lineaire regressie en AVK met para meters voor het opruimen van wille keurige forests ook gepresenteerd. Het **elastische net** is een geregelde regressie methode voor het aanpassen van lineaire regressie modellen die de L1-en L2-metrische gegevens lineair combi neren als boete van de [Lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) -en [gleuf](https://en.wikipedia.org/wiki/Tikhonov_regularization) -methoden.   

<!-- -->

> [!NOTE]
> Hoewel de Spark MLlib Toolkit is ontworpen om te werken met grote gegevens sets, wordt hier voor het gemak een relatief klein voor beeld (ongeveer 30 MB met 170K-0,1 rijen) gebruikt. De hier opgegeven oefening wordt efficiënt uitgevoerd (in ongeveer 10 minuten) op een HDInsight-cluster met twee worker-knoop punten. Dezelfde code, met kleine wijzigingen, kan worden gebruikt voor het verwerken van grotere gegevens sets, met de juiste wijzigingen voor het opslaan van gegevens in het geheugen en het wijzigen van de cluster grootte.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Instellen: Spark-clusters en-notebooks
In dit overzicht vindt u instructies en code voor gebruik van een HDInsight Spark 1.6-cluster. Er zijn echter ook Jupyter-notebooks voor zowel HDInsight Spark 1.6- als Spark 2.0-clusters. Ga naar de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub-opslagplaats met de notebooks voor een beschrijving van de notebooks en koppelingen ernaartoe. De code in dit overzicht en in de gekoppelde notebooks is bovendien generiek en moet op ieder Spark-cluster werken. Als u geen gebruikmaakt van HDInsight Spark, kunnen de stappen voor het instellen en beheren van het cluster enigszins afwijken van wat hier wordt beschreven. Voor het gemak kunt u de koppelingen naar de Jupyter-notebooks voor Spark 1,6 en 2,0 uitvoeren in de pyspark-kernel van de Jupyter Notebook-server:

### <a name="spark-16-notebooks"></a>Spark 1,6-notebooks

[pySpark-machine learning-data-Science-Spark-Advanced-Data-Explore-Modeling. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): bevat onderwerpen in notebook #1 en model ontwikkeling met afstemming tuning en kruislings validatie.

### <a name="spark-20-notebooks"></a>Spark 2,0-notebooks

[Spark 2.0-pySpark3-machine-learning-data-wetenschappen-Spark-Advanced-Data-Explore-Modeling. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): dit bestand bevat informatie over het verkennen, model leren en scoren van gegevens in Spark 2,0-clusters.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Setup: opslag locaties, Bibliotheken en de vooraf ingestelde Spark-context
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
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**UITVOER:**

DateTime. datetime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Bibliotheken importeren
Importeer de benodigde bibliotheken met de volgende code:

```python
# LOAD PYSPARK LIBRARIES
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

Zie [kernels die beschikbaar zijn voor Jupyter-notebooks met Hdinsight Spark Linux-clusters in hdinsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)voor meer informatie over de kernels voor Jupyter-notebooks en de vooraf gedefinieerde ' magics ' die ze bieden.

## <a name="data-ingestion-from-public-blob"></a>Gegevens opname vanuit open bare blob:
De eerste stap in het data Science-proces is het opnemen van de gegevens die moeten worden geanalyseerd uit bronnen waar deze zich bevinden in uw omgeving voor het verkennen van gegevens en modellen. Deze omgeving is Spark in dit overzicht. Deze sectie bevat de code voor het voltooien van een reeks taken:

* het gegevens-sample opnemen dat moet worden gemodelleerd
* de invoer-gegevensset lezen (opgeslagen als een TSV-bestand)
* de gegevens opmaken en opschonen
* objecten (RDD's of dataframes) maken en in het cachegeheugen opslaan
* registreren als een tijdelijke tabel (temp-table) in SQL-context

Dit is de code voor gegevensopname.

```python
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

# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
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

Benodigde tijd voor het uitvoeren van de cel: 276,62 seconden

## <a name="data-exploration--visualization"></a>& visualisatie voor het verkennen van gegevens
Zodra de gegevens zijn overgebracht naar Spark, is de volgende stap in het datascience-proces het verkrijgen van een dieper inzicht van de gegevens door deze te verkennen en visualiseren. In deze sectie onderzoeken we de taxigegevens met behulp van SQL-query's en plotten we de doelvariabelen en potentiële functies voor visuele inspectie. We gaan met name de frequentie van het aantal passagiers in taxiritten plotten, de frequentie van fooien en hoe fooien variëren per bedrag en type betaling.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Een histogram plotten van aantal passagiers in het sample met taxiritten
Deze code en volgende fragmenten gebruiken SQL-magic om een query op het sample uit te voeren en local-magic om de gegevens te plotten.

* **SQL-magic (`%%sql`)** De HDInsight PySpark-kernel ondersteunt eenvoudige inline HiveQL-query's in de sqlContext. Het argument (-o NAAM_VARIABELE) zorgt ervoor dat de uitvoer van de SQL-query als een Panda-dataframe persistent wordt gemaakt op de Jupyter-server. Dit betekent dat het beschikbaar is in de lokale modus.
* De **magic** `%%local` wordt gebruikt om code lokaal uit te voeren op de Jupyter-server, die het hoofdknooppunt van het HDInsight-cluster vormt. Normaal gesp roken gebruikt u `%%local` Magic nadat het `%%sql -o` Magic wordt gebruikt om een query uit te voeren. Met de para meter-o wordt de uitvoer van de SQL-query lokaal opgeslagen. Vervolgens wordt `%%local` de volgende set code fragmenten geactiveerd om lokaal te worden uitgevoerd op basis van de uitvoer van de SQL-query's die lokaal zijn opgeslagen. De uitvoer wordt automatisch gevisualiseerd nadat u de code hebt uitgevoerd.

Met deze query worden de ritten opgehaald op basis van het aantal passagiers. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

Deze code maakt een lokaal dataframe van de queryuitvoer en plot de gegevens. Met de magic `%%local` wordt een lokaal dataframe gemaakt, `sqlResults`, dat kan worden gebruikt voor plotten met matplotlib. 

<!-- -->

> [!NOTE]
> Deze PySpark-magic wordt meerdere keren gebruikt in dit overzicht. Als de hoeveelheid gegevens groot is, kunt u het beste een sample nemen om een dataframe te maken dat in het lokale geheugen past.

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Dit is de code voor het plotten van de ritten op aantallen passagiers:

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT PASSENGER NUMBER VS TRIP COUNTS
x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**UITVOER:**

![Frequentie van de trips op het aantal reizigers](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

U kunt kiezen uit verschillende soorten visualisaties (tabel, cirkel, lijn, vlak of staaf) met behulp van de **Type**-knoppen in het notebook. Hier ziet u een staafdiagram.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Een histogram plotten van fooien en hoe fooien verschillen per aantal passagiers en ritbedragen
Een SQL-query gebruiken om gegevens te bemonsteren...

```sql
# SQL SQUERY
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
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline

# TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount ($) by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
ax.set_title('Tip amount by Fare amount ($)')
ax.set_xlabel('Fare Amount')
ax.set_ylabel('Tip Amount')
plt.axis([-2, 120, -2, 30])
plt.show()
```

**UITVOER:** 

![Verdeling van fooien](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Hoogte van fooi per aantal passagiers](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Aantal fooien per tarief](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Functie techniek, trans formatie en gegevens voorbereiding voor model lering
In deze sectie wordt de code beschreven voor procedures die worden gebruikt om gegevens voor te bereiden voor gebruik in ML-modellering. Er wordt uitgelegd hoe u de volgende taken uitvoert:

* Een nieuwe functie maken door het partitioneren van uren in de verkeers tijd bakken
* Index-en on-categorische functies voor code ring
* Gelabelde puntobjecten maken voor invoer in ML-functies
* Een willekeurige subsampling van de gegevens maken en deze splitsen in trainings- en testsets
* Functie schalen
* Objecten opslaan in cachegeheugen

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Een nieuwe functie maken door het aantal verkeer naar opslag locaties te partitioneren
Deze code laat zien hoe u een nieuwe functie maakt door verkeer naar opslag locaties te partitioneren en vervolgens te bepalen hoe het resulterende gegevens frame in het geheugen moet worden opgeslagen. Caching leidt tot een betere uitvoerings tijd waarbij flexibele, gedistribueerde gegevens sets (Rdd's) en data-frames herhaaldelijk worden gebruikt. Daarom slaan we Rdd's-en data-frames op in verschillende fasen in dit overzicht.

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
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**UITVOER:**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index en categorische-functies met één Hot-code ring
In deze sectie wordt beschreven hoe u categorische functies indexeert of codeert voor invoer in de modelleerfuncties. De functies voor model leren en voors pellen van MLlib vereisen dat onderdelen met categorische-invoer gegevens worden geïndexeerd of gecodeerd voordat ze kunnen worden gebruikt. 

Afhankelijk van het model moet u deze op verschillende manieren indexeren of coderen. Logistieke en lineaire regressie modellen vereisen bijvoorbeeld een hot-code ring, waarbij bijvoorbeeld een functie met drie categorieën kan worden uitgebreid naar drie functie kolommen, waarbij elk 0 of 1 is, afhankelijk van de categorie van een waarneming. MLlib biedt de functie [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) voor het uitvoeren van one-hot codering. Met dit coderingsprogramma wordt een kolom met labelindexen toegewezen aan een kolom met binaire vectoren, met maximaal één een-waarde. Met deze codering kunnen algoritmen die functies met numerieke waarden verwachten, zoals logistieke regressie, worden toegepast op categorische-functies.

Dit is de code voor het indexeren en coderen van categorische-functies:

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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

Benodigde tijd voor het uitvoeren van de cel: 3,14 seconden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gelabelde puntobjecten maken voor invoer in ML-functies
Deze sectie bevat code die laat zien hoe u categorische tekst gegevens indexeert als een gelabeld punt gegevens type en hoe u dit kunt coderen. Deze trans formatie bereidt tekst gegevens voor die moeten worden gebruikt voor het trainen en testen van MLlib en andere classificatie modellen. Gelabelde puntobjecten zijn RDD's die zijn opgemaakt als invoergegevens voor de meeste ML-algoritmen in MLlib. Een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, hetzij dense of sparse, die is gekoppeld aan een label/respons.

Hier volgt de code voor het indexeren en coderen van tekstfuncties voor binaire classificatie.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Met deze code neemt u een willekeurig sample van de gegevens (25% wordt hier gebruikt). Hoewel dit voor beeld niet vereist is vanwege de grootte van de gegevensset, laten we zien hoe u de gegevens hier kunt bemonsteren. Vervolgens weet u hoe u dit zo nodig kunt gebruiken voor uw eigen probleem. Wanneer samples groot zijn, kan sampling aanzienlijk tijd besparen bij het trainen van modellen. Vervolgens splitsen we het sample in een trainingsdeel (75% hier) en een testdeel (25% hier) voor gebruik in de classificatie- en regressiemodellen.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
from pyspark.sql.functions import rand

samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# CACHE TRAIN AND TEST DATA
trainData.cache()
testData.cache()

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

Benodigde tijd voor het uitvoeren van de cel: 0,31 seconde

### <a name="feature-scaling"></a>Functie schalen
Het schalen van functies, ook wel bekend als gegevensnormalisatie, zorgt ervoor dat functies met weid verspreide waarden geen buitensporige weging krijgen toegewezen in de doelfunctie. De code voor functieschaling maakt gebruik van de [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) om de functies te schalen op eenheidvariantie. Het wordt verschaft door MLlib voor gebruik in lineaire regressie met stochastische Gradient Daal (SGD). SGD is een populair algoritme voor het trainen van een breed scala aan andere machine learning modellen, zoals geregelde regressieën of Support Vector machines (SVM).   

> [!TIP]
> We hebben vastgesteld dat het algoritme LinearRegressionWithSGD gevoelig is functieschaling.   
> 
> 

Hier volgt de code voor het schalen van variabelen voor gebruik met het geregulariseerde lineaire SGD-algoritme.

```python
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

Benodigde tijd voor het uitvoeren van de cel: 11,67 seconden

### <a name="cache-objects-in-memory"></a>Objecten opslaan in cachegeheugen
De tijd die nodig is voor het trainen en testen van ML-algoritmen kan worden beperkt door de invoer gegevens frame objecten in de cache te plaatsen die worden gebruikt voor classificatie, regressie en geschaalde functies.

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

Benodigde tijd voor het uitvoeren van de cel: 0,13 seconde

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Voors pellen of een tip wordt betaald met binaire classificatie modellen
In deze sectie wordt beschreven hoe u drie modellen gebruikt om via binaire classificatie te voorspellen of er al dan niet een fooi wordt gegeven voor een taxirit. Het betreft deze modellen:

* Logistieke regressie 
* Random forest
* Gradient Boosting Trees

Elke sectie voor het ontwikkelen van een model is onderverdeeld in stappen: 

1. **Modelgegevens trainen** met één parameterset
2. **Modelevaluatie** met een testgegevensset met metrische gegevens
3. **Model opslaan** in blob voor toekomstig gebruik

We laten u zien hoe u kruis validatie (AVK) met para meters op twee manieren kunt verruimen:

1. Het gebruik van **algemene** aangepaste code die kan worden toegepast op elk algoritme in MLlib en op alle parameter sets in een algoritme. 
2. Met de **pijplijn functie PySpark CrossValidator**. CrossValidator heeft enkele beperkingen voor Spark 1.5.0: 
   
   * Pijplijn modellen kunnen niet worden opgeslagen of bewaard voor toekomstig gebruik.
   * Kan niet worden gebruikt voor elke para meter in een model.
   * Kan niet worden gebruikt voor elk MLlib-algoritme.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Algemene kruis validatie en afstemming-leegmaak handelingen die worden gebruikt met het logistiek-algoritme voor binaire classificatie
De code in deze sectie laat zien hoe u een logistiek regressiemodel traint, evalueert en opslaat met [BFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm), een algoritme dat voorspelt of er al dan niet een fooi wordt gegeven voor een taxirit in de gegevensset over taxigebruik in New York. Het model wordt getraind met kruis validatie (AVK) en afstemming sweep, geïmplementeerd met aangepaste code die kan worden toegepast op elk van de leer algoritmen in MLlib.   

<!-- -->

> [!NOTE]
> Het uitvoeren van deze aangepaste CV-code kan enkele minuten duren.

<!-- -->

**Het logistieke regressiemodel trainen met CV en sweeping van hyperparameters**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from pyspark.mllib.evaluation import BinaryClassificationMetrics

# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
from sklearn.grid_search import ParameterGrid
grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
paramGrid = list(ParameterGrid(grid))
numModels = len(paramGrid)

# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
nFolds = 3;
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

# BEGIN CV WITH PARAMETER SWEEP
for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create LabeledPoints from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowOneHotBinary)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        regt = paramGrid[j]['regType']
        regp = paramGrid[j]['regParam']
        iters = paramGrid[j]['iterations']
        tol = paramGrid[j]['tolerance']
        # Train logistic regression model with hypermarameter set
        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                  regParam=regp, tolerance = tol, intercept=True)
        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
        # Use ROC-AUC as accuracy metrics
        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
        metric = validMetrics.areaUnderROC
        metricSum[j] += metric

avgAcc = metricSum / nFolds;
bestParam = paramGrid[np.argmax(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                              iterations=bestParam['iterations'], 
                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                              intercept=True)


# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitBest.weights))
print("Intercept: " + str(logitBest.intercept))

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

De code in deze sectie laat zien hoe u een logistiek regressie model evalueert op basis van een test gegevensverzameling, met inbegrip van een tekening van de ROC-curve.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT LIBRARIES
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Opper vlak onder PR = 0.985336538462

Gebied onder ROC = 0.983383274312

Overall Statistics

Precisie = 0.984174341679

Intrekken = 0.984174341679

F1-score = 0.984174341679

Benodigde tijd voor het uitvoeren van de cel: 2,67 seconden

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

#PREDICTIONS
predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVES
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

![Logistieke regressie-curve voor algemene aanpak](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Model in een BLOB persistent maken voor toekomstig verbruik**

De code in deze sectie laat zien hoe u het logistiek regressie model voor verbruik opslaat.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

# PERSIST MODEL
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;

logitBest.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 34,57 seconden

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>De CrossValidator-pijplijn functie van MLlib gebruiken met een logistiek regressie model (elastisch regressie)
De code in deze sectie laat zien hoe u een logistiek regressiemodel traint, evalueert en opslaat met [BFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm), een algoritme dat voorspelt of er al dan niet een fooi wordt gegeven voor een taxirit in de gegevensset over taxigebruik in New York. Het model wordt getraind met kruis validatie (AVK) en afstemming-verruiming geïmplementeerd met de MLlib CrossValidator-pijplijn functie voor AVK met parameter sweep.   

<!-- -->

> [!NOTE]
> Het uitvoeren van deze MLlib AVK-code kan enkele minuten duren.

<!-- -->

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from sklearn.metrics import roc_curve,auc

# DEFINE ALGORITHM / MODEL
lr = LogisticRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build()

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=BinaryClassificationEvaluator(),
                    numFolds=3)

# CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


## PREDICT AND EVALUATE ON TEST DATA-SET

# USE TEST DATASET FOR PREDICTION
testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
test_predictions = cv_model.transform(testDataFrame)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
``` 

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 107,98 seconden

**De ROC-curve plotten**

*predictionAndLabelsDF* wordt geregistreerd als een tabel, *tmp_results*, in de vorige cel. *tmp_results* kan worden gebruikt om query's uit te voeren en resultaten uit te voeren in het dataframe sqlResults voor plotten. Dit is de code.

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

Hier volgt de code voor het uitzetten van de ROC-curve.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
%%local
from sklearn.metrics import roc_curve,auc

# ROC CURVE
prob = [x["values"][1] for x in sqlResults["probability"]]
fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

#PLOT
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

![De MLlib-curve van logistiek regressie met behulp van CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Random forest-classificatie
De code in deze sectie laat zien hoe u een wille keurige regressie kunt trainen, evalueren en opslaan waarmee wordt gedicteerd of een tip wordt betaald voor een reis in de NYCe taxi en ritbedrag-gegevensset.

```python
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
## UN-COMMENT IF YOU WANT TO PRING TREES
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

Gebied onder ROC = 0.985336538462

Benodigde tijd voor het uitvoeren van de cel: 26,72 seconden

### <a name="gradient-boosting-trees-classification"></a>Gradient Boosting Trees-classificatie
De code in deze sectie laat zien hoe u een Gradient Boosting Trees-model traint, evalueert en opslaat met BFGS, een algoritme dat voorspelt of er al dan niet een fooi wordt gegeven voor een taxirit in de gegevensset over taxigebruik in New York.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                numIterations=10)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# Area under ROC curve
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

Gebied onder ROC = 0.985336538462

Benodigde tijd voor het uitvoeren van de cel: 28,13 seconden

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Aantal fooien voor spel met regressie modellen (geen gebruik van AVK)
In deze sectie wordt uitgelegd hoe u drie modellen voor de regressie taak gebruikt: voor spelt u het fooien bedrag dat is betaald voor een taxi op basis van andere tip-functies. Het betreft deze modellen:

* Gereguleerde lineaire regressie
* Random forest
* Gradient Boosting Trees

Deze modellen zijn beschreven in de inleiding. Elke sectie voor het ontwikkelen van een model is onderverdeeld in stappen: 

1. **Modelgegevens trainen** met één parameterset
2. **Modelevaluatie** met een testgegevensset met metrische gegevens
3. **Model opslaan** in blob voor toekomstig gebruik   

<!-- -->

> [!NOTE] 
> Kruis validatie wordt niet gebruikt in combi natie met de drie regressie modellen in deze sectie, omdat deze in detail zijn opgenomen voor de logistieke regressie modellen. In de bijlage van dit onderwerp vindt u een voor beeld waarin wordt getoond hoe u AVK met elastisch net voor lineaire regressie kunt gebruiken.

<!-- -->

<!-- -->

> [!NOTE] 
> In onze ervaring kan er sprake zijn van problemen met de convergentie van LinearRegressionWithSGD-modellen en moeten de para meters zorgvuldig worden gewijzigd/geoptimaliseerd voor het verkrijgen van een geldig model. Het schalen van variabelen is zeer nuttig voor convergentie. Een elastische net-regressie, weer gegeven in de bijlage van dit onderwerp, kan ook worden gebruikt in plaats van LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Lineaire regressie met SGD
De code in deze sectie laat zien hoe u geschaalde functies gebruikt om een lineaire regressie te trainen die gebruikmaakt van SGD (Stochastic Gradient Descent) voor optimalisatie, en hoe u het model kunt beoordelen, evalueren en opslaan in Azure Blob Storage (WASB).

> [!TIP]
> In onze ervaring kunnen er problemen optreden met de convergentie van LinearRegressionWithSGD-modellen en moeten parameters zorgvuldig worden gewijzigd/geoptimaliseerd voor het verkrijgen van een geldig model. Het schalen van variabelen is zeer nuttig voor convergentie.
> 
> 

```python
# LINEAR REGRESSION WITH SGD 

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

print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
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

Coëfficiënten: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Interceptie: 0.854507624459

RMSE = 1.23485131376

R-Sqr = 0.597963951127

Benodigde tijd voor het uitvoeren van de cel: 38,62 seconden

### <a name="random-forest-regression"></a>Random Forest-regressie
De code in deze sectie laat zien hoe u een wille keurig forest model traint, evalueert en opslaat waarmee fooien voor de taxi reis gegevens worden voor speld.   

<!-- -->

> [!NOTE]
> Kruis validatie met parameter opruimen met aangepaste code vindt u in de bijlage.

<!-- -->

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
# UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

RMSE = 0.931981967875

R-Sqr = 0.733445485802

Benodigde tijd voor het uitvoeren van de cel: 25,98 seconden

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

# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

# EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
test_predictions= sqlContext.createDataFrame(predictionAndLabels)
test_predictions_pddf = test_predictions.toPandas()

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

RMSE = 0.928172197114

R-Sqr = 0.732680354389

Benodigde tijd voor het uitvoeren van de cel: 20,9 seconden

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
import numpy as np

# PLOT
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 15])
plt.show(ax)
```

![Werkelijke versus voorspelde fooien](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Bijlage: aanvullende regressie taken met kruis validatie met parameter sweeps
Deze bijlage bevat code waarin wordt getoond hoe u met behulp van elastisch net voor lineaire regressie een gebruik maakt van een flexibele, en hoe u dit kunt doen met behulp van para meters voor een wille keurige regressie met aangepaste code.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Kruis validatie met elastisch net voor lineaire regressie
De code in deze sectie laat zien hoe u kruis validatie kunt uitvoeren met elastisch net voor lineaire regressie en hoe u het model kunt evalueren op basis van test gegevens.

```python
###  CV USING ELASTIC NET FOR LINEAR REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.regression import LinearRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import RegressionEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

# DEFINE ALGORITHM/MODEL
lr = LinearRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build() 

# DEFINE PIPELINE 
# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
pipeline = Pipeline(stages=[lr])

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=RegressionEvaluator(),
                    numFolds=3)

# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


# EVALUATE MODEL ON TEST SET
testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

# MAKE PREDICTIONS ON TEST DOCUMENTS
# cvModel uses the best model found (lrModel).
predictionAndLabels = cv_model.transform(testDataFrame)

# CONVERT TO DF AND SAVE REGISTER DF AS TABLE
predictionAndLabels.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 161,21 seconden

**Evalueren met R-SQR-metriek**

*tmp_results* wordt geregistreerd als een Hive-tabel in de vorige cel. Resultaten uit de tabel worden uitgevoerd in het dataframe *sqlResults* om te worden geplot. Dit is de code:

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

Hier volgt de code voor het berekenen van R-Sqr.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**UITVOER:**

R-Sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Kruis validatie met parameter opruimen met aangepaste code voor regressie van wille keurig forest
De code in deze sectie laat zien hoe u kruis validatie met parameter opruiming kunt uitvoeren met aangepaste code voor regressie van wille keurig forest en hoe u het model kunt evalueren op basis van test gegevens.

```python
# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics
from sklearn.grid_search import ParameterGrid

## CREATE PARAMETER GRID
grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
paramGrid = list(ParameterGrid(grid))

## SPECIFY LEVELS OF CATEGORICAL VARIBLES
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
nFolds = 3;
numModels = len(paramGrid)
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create labeled points from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowIndexingRegression)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        maxD = paramGrid[j]['maxDepth']
        numT = paramGrid[j]['numTrees']
        # Train logistic regression model with hypermarameter set
        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=numT, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=maxD, maxBins=32)
        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
        # Use ROC-AUC as accuracy metrics
        validMetrics = RegressionMetrics(predictionAndLabels)
        metric = validMetrics.rootMeanSquaredError
        metricSum[j] += metric

avgAcc = metricSum/nFolds;
bestParam = paramGrid[np.argmin(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

## TRAIN FINAL MODL WIHT BEST PARAMETERS
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

# EVALUATE MODEL ON TEST DATA
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

#PRINT TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UITVOER:**

RMSE = 0.906972198262

R-Sqr = 0.740751197012

Benodigde tijd voor het uitvoeren van de cel: 69,17 seconden

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Objecten opschonen van geheugen en model locaties afdrukken
Gebruik `unpersist()` om objecten te verwijderen die in het cachegeheugen zijn opgeslagen.

```python
# UNPERSIST OBJECTS CACHED IN MEMORY

# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()
trainData.unpersist()
trainData.unpersist()

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

**UITVOER:**

PythonRDD [122] op RDD op PythonRDD. scala: 43

* * Uitvoer traject naar model bestanden dat moet worden gebruikt in het verbruiks notitieblok. * * Als u een onafhankelijke gegevensverzameling wilt gebruiken en scoren, moet u deze bestands namen kopiëren en plakken in het ' verbruiks notitieblok '.

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**UITVOER:**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0316 _47_ 30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0316 _51_ 28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0316 _50_ 17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0316 _51_ 57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0316 _50_ 40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0316 _52_ 18.827237"

## <a name="whats-next"></a>Volgende stappen
U hebt nu regressie- en classificatiemodellen gemaakt met de Spark MlLib en bent dus klaar om te leren hoe u deze modellen kunt beoordelen en evalueren.

**Modelverbruik:** Voor meer informatie over het beoordelen en evalueren van de classificatie- en regressiemodellen die in dit onderwerp zijn gemaakt, raadpleegt u [Operationalize Spark-built machine learning models](spark-model-consumption.md) (In Spark gebouwde machine learning-modellen operationeel maken).
