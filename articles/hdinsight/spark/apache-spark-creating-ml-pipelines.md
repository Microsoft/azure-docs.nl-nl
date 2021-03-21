---
title: Apache Spark machine learning pijp lijn maken-Azure HDInsight
description: Gebruik de Apache Spark machine learning bibliotheek om gegevens pijplijnen te maken in azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 07/22/2019
ms.openlocfilehash: e9897c7ba14e6190698cf10792a94acc759699d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940189"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Een machine learning-pijplijn in Apache Spark maken

Met de schaal bare machine learning-bibliotheek van Apache Spark (MLlib) worden model functies naar een gedistribueerde omgeving gebracht. Het Spark-pakket [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) is een set api's op hoog niveau die is gebouwd op DataFrames. Met deze Api's kunt u praktische pijp lijnen voor machine learning maken en afstemmen.  *Spark machine learning*  verwijst naar deze MLlib data frame-API, niet op de oudere rdd-API voor pijp lijnen.

Een machine learning (ML) pijp lijn is een volledige werk stroom waarmee meerdere machine learning algoritmen samen worden gecombineerd. Er kunnen veel stappen worden uitgevoerd voor het verwerken en leren van gegevens, waardoor een reeks algoritmen vereist is. Met pijp lijnen worden de fasen en de volg orde van een machine learning proces gedefinieerd. In MLlib worden fasen van een pijp lijn vertegenwoordigd door een specifieke reeks PipelineStages, waarbij een transformator en een Estimator elke taak uitvoeren.

Een transformator is een algoritme waarmee een data frame met behulp van de-methode wordt omgezet in een andere `transform()` . Een functie transformator kan bijvoorbeeld één kolom van een data frame lezen, deze toewijzen aan een andere kolom en een nieuwe data frame uitvoer met de toegewezen kolom eraan toe te voegen.

Een Estimator is een abstractie van leer algoritmen en is verantwoordelijk voor het aanpassen of trainen van een gegevensset om een transformator te maken. Een Estimator implementeert een methode met de naam `fit()` , die een data frame accepteert en een data frame, een transformator, produceert.

Elk stateless exemplaar van een transformator of een Estimator heeft een eigen unieke id, die wordt gebruikt bij het opgeven van para meters. Beide gebruiken een uniforme API voor het opgeven van deze para meters.

## <a name="pipeline-example"></a>Voor beeld van pijp lijn

Om een praktisch gebruik van een ML-pijp lijn te demonstreren, gebruikt dit voor beeld het voorbeeld `HVAC.csv` gegevensbestand dat vooraf is geladen in de standaard opslag voor uw HDInsight-cluster, hetzij Azure Storage als data Lake Storage. Als u de inhoud van het bestand wilt bekijken, gaat u naar de `/HdiSamples/HdiSamples/SensorSampleData/hvac` map. `HVAC.csv` bevat een set keer met zowel doel-als werkelijke Tempe raturen voor systemen voor airco (*Verwarming, ventilatie en airconditioning*) in verschillende gebouwen. Het doel is om het model op de gegevens te trainen en een prognose temperatuur te maken voor een bepaald gebouw.

De volgende code:

1. Hiermee definieert u een `LabeledDocument` , waarin de `BuildingID` , `SystemInfo` (de id en leeftijd van het systeem) en een `label` (1,0 als het gebouw te warm is, 0,0 anders) wordt opgeslagen.
2. Hiermee maakt u een aangepaste parser `parseDocument` -functie die een regel (rij) van gegevens neemt en bepaalt of het gebouw ' hot ' is door de doel temperatuur te vergelijken met de werkelijke Tempe ratuur.
3. Past de parser toe bij het uitpakken van de bron gegevens.
4. Maakt trainings gegevens.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument


def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)


# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Deze voorbeeld pijplijn heeft drie fasen: `Tokenizer` en `HashingTF` (beide trans formats) en `Logistic Regression` (een Estimator).  De geëxtraheerde en geparseerde gegevens in de `training` Data frame stromen via de pijp lijn wanneer deze `pipeline.fit(training)` wordt aangeroepen.

1. De eerste fase, `Tokenizer` , splitst de `SystemInfo` invoer kolom (die bestaat uit de systeem-id en de leeftijds waarden) in een `words` uitvoer kolom. Deze nieuwe `words` kolom wordt toegevoegd aan de data frame. 
2. De tweede fase, `HashingTF` , converteert de nieuwe `words` kolom naar functie vectoren. Deze nieuwe  `features` kolom wordt toegevoegd aan de data frame. Deze eerste twee fasen zijn trans formats. 
3. De derde fase, `LogisticRegression` , is een Estimator, en de pijp lijn roept de `LogisticRegression.fit()` methode voor het maken van een aan `LogisticRegressionModel` . 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Als u de nieuwe `words` en kolommen wilt zien die `features` zijn toegevoegd door de `Tokenizer` `HashingTF` -en-trans formaties en een voor beeld van de `LogisticRegression` Estimator, voert u een `PipelineModel.transform()` methode uit op de oorspronkelijke data frame. In productie code moet de volgende stap worden door gegeven in een test data frame om de training te valideren.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

Het `model` object kan nu worden gebruikt voor het maken van voor spellingen. Zie voor het volledige voor beeld van deze machine learning-toepassing en stapsgewijze instructies voor het uitvoeren van het programma [Apache Spark machine learning-toepassingen in azure HDInsight bouwen](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Zie ook

* [Data Science met scala en Apache Spark op Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
