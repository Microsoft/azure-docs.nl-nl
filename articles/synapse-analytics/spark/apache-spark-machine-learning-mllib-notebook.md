---
title: 'Zelfstudie: Een machine learning-app bouwen met Apache Spark MLlib'
description: Een zelf studie over het gebruik van Apache Spark MLlib voor het maken van een machine learning-app die een gegevensset analyseert met behulp van classificatie via logistieke regressie.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 5caa41b852bf55a11489db6c0bab871b20720e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670666"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Zelfstudie: Een machine learning-app bouwen met Apache Spark MLlib en Azure Synapse Analytics

In dit artikel leert u hoe u Apache Spark [MLlib](https://spark.apache.org/mllib/) kunt gebruiken om een machine learning-toepassing te maken die eenvoudige voorspellende analyse uitvoert voor Azure Open Datasets. Spark biedt ingebouwde machine learning-bibliotheken. In dit voorbeeld wordt gebruikgemaakt van *classificatie* via logistieke regressie.

SparkML en MLlib zijn kern Spark-bibliotheken die een groot aantal hulpprogram ma's bieden die handig zijn voor het machine learning van taken, waaronder hulpprogram ma's die geschikt zijn voor:

- Classificatie
- Regressie
- Clustering
- Modellering van onderwerpen
- SVD (Singular Value Decomposition) en PCA (Principal Component Snalysis)
- Hypothesen voor het testen en berekenen van voorbeeldstatistieken

## <a name="understand-classification-and-logistic-regression"></a>Classificatie en logistieke regressie begrijpen

*Classificatie*, een populaire machine learning-taak, is het proces waarbij invoergegevens worden gesorteerd in categorieën. Het is de taak van een classificatie algoritme om te bepalen hoe *labels* moeten worden toegewezen aan invoer gegevens die u opgeeft. U kunt bijvoorbeeld zien wat een machine learning-algoritme is dat aandelen informatie als invoer accepteert en het aandeel opsplitst in twee categorieën: aandelen die u moet verkopen en aandelen die u moet houden.

*Logistieke regressie* is een algoritme dat u kunt gebruiken voor classificatie. De logistieke regressie-API van Spark is handig voor *binaire classificatie*, of voor het classificeren van invoergegevens in één van twee groepen. [Zie voor](https://en.wikipedia.org/wiki/Logistic_regression)meer informatie over logistiek regressie.

In samen vatting produceert het proces van logistiek regressie een *logistiek functie* die u kunt gebruiken om de kans te voors pellen dat een invoer vector deel uitmaakt van de ene groep of de andere.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Voor beeld van voorspellende analyse op NYC taxi-gegevens

In dit voor beeld gebruikt u Spark voor het uitvoeren van een bepaalde voorspellende analyse op basis van gegevens van de taxi-fooi van New York. De gegevens zijn beschikbaar via [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Deze subset van de gegevensset bevat informatie over taxiritten, waaronder informatie over elke rit, de begin- en eindtijd en locaties, de kosten, en andere interessante kenmerken.

> [!IMPORTANT]
> Er kunnen extra kosten gelden voor het ophalen van deze gegevens uit de opslaglocatie.

In de volgende stappen ontwikkelt u een model om te voorspellen of voor een bepaalde trip een fooi is betaald of niet.

## <a name="create-an-apache-spark-machine-learning-model"></a>Een Apache Spark machine learning model maken

1. Maak een notebook met behulp van de PySpark-kernel. Zie [Een notebook maken](../quickstart-apache-spark-notebook.md#create-a-notebook) voor instructies.
2. Importeer de typen die zijn vereist voor deze toepassing. Kopieer en plak de volgende code in een lege cel en druk op SHIFT + ENTER. Of voer de cel uit met behulp van het blauwe afspeel pictogram links van de code.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Vanwege de PySpark-kernel hoeft u niet expliciet contexten te maken. De Spark-context wordt automatisch voor u gemaakt wanneer u de eerste codecel uitvoert.

## <a name="construct-the-input-dataframe"></a>De invoer-data frame maken

Omdat de onbewerkte gegevens zich in een Parquet-indeling bevindt, kunt u de Spark-context gebruiken om het bestand rechtstreeks in het geheugen te halen als een data frame. Hoewel de code in de volgende stappen de standaard opties gebruikt, is het mogelijk om zo nodig de toewijzing van gegevens typen en andere schema kenmerken af te dwingen.

1. Voer de volgende regels uit om een Spark-data frame te maken door de code in een nieuwe cel te plakken. Met deze stap worden de gegevens opgehaald via de API open data sets. Het ophalen van deze gegevens genereert bijna 1,5 miljard rijen. 

   Afhankelijk van de grootte van uw serverloze Apache Spark pool, kunnen de onbewerkte gegevens te groot zijn of te veel tijd in beslag nemen. U kunt deze gegevens filteren naar een kleinere hoeveelheid. Het volgende code voorbeeld maakt gebruik van `start_date` en `end_date` om een filter toe te passen dat één maand aan gegevens retourneert.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Het nadeel van eenvoudige filtering is dat, vanuit een statistisch perspectief, het mogelijk is om de gegevens te verrijken. Een andere aanpak is het gebruik van de steekproeven die zijn ingebouwd in Spark. 

   Met de volgende code wordt de gegevensset beperkt tot ongeveer 2.000 rijen, indien deze na de voor gaande code worden toegepast. U kunt deze steekproef stap gebruiken in plaats van het eenvoudige filter of in combi natie met het eenvoudige filter.

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Het is nu mogelijk om de gegevens te bekijken om te zien wat er is gelezen. Normaal gesp roken is het beter om gegevens met een subset te bekijken in plaats van de volledige set, afhankelijk van de grootte van de gegevensset. 

   De volgende code biedt twee manieren om de gegevens weer te geven. De eerste manier is Basic. De tweede manier biedt een veel rijkere raster ervaring, samen met de mogelijkheid om de gegevens grafisch te visualiseren.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. Afhankelijk van de grootte van de gegenereerde gegevensset en uw nood zaak om het notitie blok te experimenteren of uit te voeren, kunt u de gegevensset lokaal in de cache opslaan in de werk ruimte. Er zijn drie manieren om expliciete caching uit te voeren:

   - Sla het data frame lokaal op als een bestand.
   - Sla het data frame op als een tijdelijke tabel of weer gave.
   - Sla het data frame op als een permanente tabel.

De eerste twee van deze benaderingen zijn opgenomen in de volgende code voorbeelden.

Het maken van een tijdelijke tabel of weer gave biedt verschillende toegangs paden naar de gegevens, maar het duurt alleen voor de duur van de Spark-sessie.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>De gegevens voorbereiden

De gegevens in de onbewerkte vorm zijn vaak niet geschikt om rechtstreeks aan een model door te geven. U moet een reeks acties op de gegevens uitvoeren om deze te verkrijgen in een staat waarin het model deze kan gebruiken.

In de volgende code voert u vier categorieën bewerkingen uit:

- Het verwijderen van uitschieters of onjuiste waarden via filteren.
- Onnodige kolommen verwijderen.
- Het maken van nieuwe kolommen die zijn afgeleid van de onbewerkte gegevens, zodat het model effectiever werkt. Deze bewerking wordt ook wel parametrisatie genoemd.
- Labels. Omdat u een binaire classificatie hebt (er is een tip of niet op een bepaalde reis), moet u het fooie bedrag converteren naar een waarde van 0 of 1.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Vervolgens maakt u een tweede doorgifte van de gegevens om de laatste functies toe te voegen.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Een logistiek regressiemodel maken

De laatste taak is het converteren van de gelabelde gegevens in een indeling die kan worden geanalyseerd via logistiek-regressie. De invoer voor een logistiek-regressie algoritme moet een set *Label/functie Vector paren* zijn, waarbij de *functie Vector* een vector is van getallen die het invoer punt vertegenwoordigen. 

Daarom moet u de categorische-kolommen converteren naar getallen. U moet in het bijzonder de `trafficTimeBins` kolommen en converteren `weekdayString` naar gehele getallen. Er zijn meerdere benaderingen voor het uitvoeren van de conversie. In het volgende voor beeld wordt gebruikgemaakt van de `OneHotEncoder` aanpak.

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Deze actie resulteert in een nieuwe data frame met alle kolommen in de juiste indeling voor het trainen van een model.

## <a name="train-a-logistic-regression-model"></a>Een logistiek regressiemodel trainen

De eerste taak is het splitsen van de gegevensset in een trainingsset en een test- of validatieset. Hier kunt u een wille keurige splitsing opgeven. Experimenteer met verschillende Splits instellingen om te zien of ze van invloed zijn op het model.

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Nu er twee DataFrames zijn, bestaat de volgende taak uit het maken van de model formule en voert u deze uit met de training data frame. Vervolgens kunt u valideren op basis van de test data frame. Experimenteer met verschillende versies van de model formule om de impact van verschillende combi Naties te bekijken.

> [!Note]
> Als u het model wilt opslaan, hebt u de Azure-rol *Storage BLOB data contributor* nodig. Ga onder uw opslag account naar **Access Control (IAM)** en selecteer **functie toewijzing toevoegen**. Wijs de rol Inzender gegevens van Blob Storage toe aan uw Azure SQL Database-Server. Deze stap kan alleen worden uitgevoerd door leden met eigenaars bevoegdheden. 
>
>Zie [deze hand leiding](../../role-based-access-control/built-in-roles.md)voor verschillende ingebouwde rollen van Azure.

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

De uitvoer van deze cel is:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Een visuele weergave van de voorspelling maken

U kunt nu een definitieve visualisatie maken om u te helpen de testresultaten te beoordelen. Een [ROC-curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is een manier om het resultaat te bekijken.

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Grafiek met de ROC-curve voor logistiek regressie in het Tip-model.](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Het Spark-exemplaar afsluiten

Wanneer u klaar bent met het uitvoeren van de toepassing, sluit u het notitie blok om de resources vrij te geven door het tabblad te sluiten. Of selecteer **sessie beëindigen** in het deel venster status aan de onderkant van het notitie blok.

## <a name="see-also"></a>Zie ook

- [Overzicht: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor .NET voor Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
- [Officiële documentatie voor Apache Spark](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Sommige van de officiële Apache Spark documentatie is gebaseerd op het gebruik van de Spark-console, die niet beschikbaar is op Apache Spark in azure Synapse Analytics. Gebruik in plaats daarvan de ervaring met [notebooks](../quickstart-apache-spark-notebook.md) of [IntelliJ](../spark/intellij-tool-synapse.md).