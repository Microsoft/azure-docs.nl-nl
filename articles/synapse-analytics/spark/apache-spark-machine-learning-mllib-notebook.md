---
title: 'Zelfstudie: Een machine learning-app bouwen met Apache Spark MLlib'
description: Een zelfstudie over hoe u Apache Spark MLlib kunt gebruiken om een machine learning-app te maken waarmee een gegevensset wordt geanalyseerd met behulp van classificatie via logistieke regressie.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 50429696c4cbe10c4723f6d4bb9c9499d9b775c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450425"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Zelfstudie: Een machine learning-app bouwen met Apache Spark MLlib en Azure Synapse Analytics

In dit artikel leert u hoe u Apache Spark [MLlib](https://spark.apache.org/mllib/) kunt gebruiken om een machine learning-toepassing te maken die eenvoudige voorspellende analyse uitvoert voor Azure Open Datasets. Spark biedt ingebouwde machine learning-bibliotheken. In dit voorbeeld wordt gebruikgemaakt van *classificatie* via logistieke regressie.

SparkML en MLlib zijn Spark-kernbibliotheken met veel hulpprogramma's die nuttig zijn voor machine learning-taken, waaronder hulpprogramma's die geschikt zijn voor:

- Classificatie
- Regressie
- Clustering
- Modellering van onderwerpen
- SVD (Singular Value Decomposition) en PCA (Principal Component Snalysis)
- Hypothesen voor het testen en berekenen van voorbeeldstatistieken

## <a name="understand-classification-and-logistic-regression"></a>Classificatie en logistieke regressie begrijpen

*Classificatie*, een populaire machine learning-taak, is het proces waarbij invoergegevens worden gesorteerd in categorieën. Het is de taak van een algoritme voor classificatie om uit te vinden hoe *labels* moeten worden toegewezen aan invoergegevens die u opgeeft. U kunt een machine learning-algoritme bijvoorbeeld beschouwen als een algoritme dat informatie over aandelen accepteert als invoer, en de aandelen opsplitst in twee categorieën: aandelen die u beter kunt verkopen en aandelen die u beter kunt behouden.

*Logistieke regressie* is een algoritme dat u kunt gebruiken voor classificatie. De logistieke regressie-API van Spark is handig voor *binaire classificatie*, of voor het classificeren van invoergegevens in één van twee groepen. Zie [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression) voor meer informatie over logistieke regressies.

Samengevat: het proces van logistieke regressie produceert een *logistieke functie* die kan worden gebruikt om de waarschijnlijkheid te voorspellen dat een invoervector behoort in de ene of in de andere groep.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Voorbeeld van voorspellende analyse van NYC-taxigegevens

In dit voorbeeld gebruikt u Spark om een voorspellende analyse uit te voeren voor gegevens over fooien voor taxiritten in New York. De gegevens zijn beschikbaar via [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Deze subset van de gegevensset bevat informatie over taxiritten, waaronder informatie over elke rit, de begin- en eindtijd en locaties, de kosten, en andere interessante kenmerken.

> [!IMPORTANT]
> Er kunnen extra kosten gelden voor het ophalen van deze gegevens uit de opslaglocatie.

In de volgende stappen ontwikkelt u een model om te voorspellen of voor een bepaalde trip een fooi is betaald of niet.

## <a name="create-an-apache-spark--machine-learning-model"></a>Een machine learning-model in Apache Spark maken

1. Maak een notebook met behulp van de PySpark-kernel. Zie [Een notebook maken](../quickstart-apache-spark-notebook.md#create-a-notebook) voor de instructies.
2. Importeer de typen die zijn vereist voor deze toepassing. Kopieer en plak de volgende code in een lege cel, en druk vervolgens op **SHIFT + ENTER**, of voer de cel uit met behulp van het blauwe pictogram Afspelen.

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

## <a name="construct-the-input-dataframe"></a>Een invoergegevensframe maken

Omdat de onbewerkte gegevens de Parquet-indeling hebben, kunt u de Spark-context gebruiken om het bestand rechtstreeks in het geheugen te plaatsen als een gegevensframe. Hoewel voor de code hieronder de standaardopties worden gebruikt, is het mogelijk de toewijzing van gegevenstypen en andere schemakenmerken af te dwingen, indien nodig.

1. Voer de volgende regels uit om een Spark-gegevensframe te maken door de code in een nieuwe cel te plakken. Hiermee haalt u de gegevens op via de Open Datasets-API. Het ophalen van deze gegevens genereert bijna 1,5 miljard rijen. Afhankelijk van de grootte van de serverloze Apache Spark-pool kan de hoeveelheid onbewerkte gegevens te groot zijn, of kan het bewerken ervan te veel tijd in beslag nemen. U kunt deze gegevens filteren naar een kleinere hoeveelheid. In het volgende codevoorbeeld wordt gebruikgemaakt van start_date en end_date om een filter toe te passen waarmee één maand aan gegevens worden geretourneerd.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Het nadeel van eenvoudige filtering is dat het, vanuit statistisch oogpunt, kan leiden tot een bevooroordeelde kijk op de gegevens. Een andere aanpak is het gebruik van de steekproeven die zijn ingebouwd in Spark. Met de volgende code wordt de gegevensset teruggebracht naar ongeveer 2000 rijen, als deze na de bovenstaande code wordt toegepast. Deze steekproef kan worden gebruikt in plaats van, of samen met, het eenvoudige filter.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Het is nu mogelijk om de gegevens te bekijken om te zien wat er is gelezen. Normaal gesproken is het beter om een subset gegevens te beoordelen, in plaats van de volledige set, afhankelijk van de grootte van de gegevensset. De volgende code biedt twee manieren om de gegevens te bekijken: de eerste manier is vrij eenvoudig, en de tweede biedt een veel rijker raster, en ook de mogelijkheid om de gegevens in een illustratie te visualiseren.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. Afhankelijk van de grootte van de gegenereerde gegevensset, en uw noodzaak om te experimenteren of de notebook meerdere keren uit te voeren, kan het raadzaam zijn om de gegevens op te slaan in de lokale cache van de werkruimte. Er zijn drie manieren om expliciete caching uit te voeren:

   - De gegevensframe lokaal opslaan als een bestand
   - De gegevensframe opslaan als een tijdelijke tabel of weergave
   - De gegevensframe opslaan als een permanente tabel

De eerste twee van deze benaderingen zijn opgenomen in de volgende codevoorbeelden.

Het maken van een tijdelijke tabel of weergave biedt verschillende toegangspaden naar de gegevens, maar alleen zolang de Spark-sessie actief is.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>De gegevens voorbereiden

De gegevens zijn in onbewerkte vorm meestal niet geschikt om rechtstreeks te worden doorgegeven aan een model. Er moet een reeks acties worden uitgevoerd voor de gegevens, om ze een vorm te geven waarin ze kunnen worden verwerkt met het model.

In de onderstaande code worden vier klassen bewerkingen uitgevoerd:

- Filteren om uitschieters/onjuiste waarden te verwijderen.
- Onnodige kolommen verwijderen.
- Nieuwe kolommen maken die zijn afgeleid uit de onbewerkte gegevens, om ervoor te zorgen dat het model effectiever werkt. Dit wordt soms featurization genoemd.
- Labelen: omdat u een binaire classificatie maakt (wordt er wel of niet een fooi gegeven na een bepaalde rit), hoeft u het bedrag van de fooi niet te converteren naar een waarde 0 of 1.

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

De gegevens worden vervolgens een tweede keer doorgegeven om de laatste functies toe te voegen.

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

De laatste taak is het converteren van de gelabelde gegevens naar een indeling die kan worden geanalyseerd via logistieke regressie. De invoer voor een algoritme voor logistieke regressie moet een set *vectorparen voor labelfuncties* zijn, waarbij de *functievector* een vector met getallen is die het invoerpunt vertegenwoordigt. We moeten de categorische kolommen dus converteren naar getallen. De kolommen `trafficTimeBins` en `weekdayString` moeten worden geconverteerd naar gehele getallen. Er zijn meerdere methoden voor het uitvoeren van de conversie, maar in dit voorbeeld is gekozen voor de veelgebruikte methode *OneHotEncoding*.

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Deze actie resulteert in een nieuwe gegevensframe met alle kolommen in de juiste indeling voor het trainen van een model.

## <a name="train-a-logistic-regression-model"></a>Een logistiek regressiemodel trainen

De eerste taak is het splitsen van de gegevensset in een trainingsset en een test- of validatieset. De splitsing is hier willekeurig. Het verdient aanbeveling om verschillende instellingen voor splitsen uit te proberen om te zien hoe deze het model beïnvloeden.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Nu er twee gegevensframes zijn, is de volgende taak het maken van de modelformule, en deze toepassen op de trainingsgegevensframe en vervolgens op de testgegevensframe. Experimenteer met verschillende versies van de modelformule om de impact van verschillende combinaties te zien.

> [!Note]
> Als u het model wilt opslaan, moet u beschikken over de Azure-rol Bijdrager voor opslagblobgegevens. Navigeer onder uw opslagaccount naar IAM (Access Control) en selecteer **Roltoewijzing toevoegen**. Wijs de Azure-rol Bijdrager voor blobgegevens toe aan uw SQL Database-server. Alleen leden met de bevoegdheid Eigenaar kunnen deze stap uitvoeren. Raadpleeg deze [gids](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) voor verschillende ingebouwde Azure-rollen.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
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

U kunt nu een definitieve visualisatie maken om u te helpen de testresultaten te beoordelen. Een [ROC-curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is één manier om het resultaat weer te geven.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![ROC-curve voor het logistieke regressiemodel over fooien](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Het Spark-exemplaar afsluiten

Wanneer u klaar bent met uitvoeren van de toepassing, sluit u de notebook af om de resources vrij te geven door het tabblad af te sluiten of **Sessie beëindigen** te selecteren in het statuspaneel onderaan de notebook.

## <a name="see-also"></a>Zie ook

- [Overzicht: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor .NET voor Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Officiële documentatie voor Apache Spark](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Sommige van de officiële Apache Spark documentatie is gebaseerd op het gebruik van de Spark-console, die echter niet beschikbaar is in Azure Synapse Spark. Gebruik in plaats daarvan de ervaring met [notebooks](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) of [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
