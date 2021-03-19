---
title: 'Zelf studie: machine learning-toepassingen maken met micro Soft Machine Learning voor Apache Spark (preview)'
description: Meer informatie over het gebruik van micro Soft Machine Learning voor Apache Spark voor het maken van machine learning-toepassingen in azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruxu
ms.author: ruxu
ms.openlocfilehash: a3899b83133b3f951547fae0b11c044bfa85a5fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589596"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Zelf studie: machine learning-toepassingen maken met micro Soft Machine Learning voor Apache Spark (preview)

In dit artikel leert u hoe u micro Soft Machine Learning voor Apache Spark ([MMLSpark](https://github.com/Azure/mmlspark)) kunt gebruiken om machine learning-toepassingen te maken. MMLSpark breidt de gedistribueerde machine learning oplossing van Apache Spark uit door een groot aantal diep gaande hulp middelen voor het leren en data Wetenschappen toe te voegen, zoals [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) en meer.  Met MMLSpark kunt u krachtige en zeer schaal bare voorspellende en analytische modellen bouwen op basis van verschillende Spark-gegevens bronnen.
Synapse Spark biedt ingebouwde MMLSpark-Bibliotheken, waaronder:

- [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit) : bibliotheek Services voor machine learning om tekst analyse zoals sentiment analyse in tweets in te scha kelen.
- [Cognitive Services op Spark](../../cognitive-services/big-data/cognitive-services-for-big-data.md) : als u de functie van Azure Cognitive Services in SparkML-pijp lijnen wilt combi neren om oplossings ontwerp af te leiden voor cognitieve gegevens modellerings services zoals afwijkings detectie.
- [LightBGM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) : machine learning-model om het model te trainen voor voorspellende analyse, zoals detectie van gezichts-id's.
- Voorwaardelijke KNN: schaal bare KNN-modellen met voorwaardelijke Query's.
- [Http op Spark](https://github.com/Azure/mmlspark/blob/master/docs/http.md) : maakt gedistribueerde micro Services-indeling mogelijk in integratie van Spark en op http-protocol.

In deze zelf studie worden voor beelden behandeld met Azure Cognitive Services in MMLSpark voor 

- Text Analytics: hiermee haalt u het gevoel op van een verzameling zinnen.
- Computer Vision: hiermee haalt u de tags (beschrijvingen van één woord) op die aan een verzameling afbeeldingen zijn gekoppeld.
- Bing Image Search: hiermee doorzoekt u het web op afbeeldingen die betrekking hebben op een query in natuurlijke taal.
- Anomaly Detector: hiermee worden afwijkingen binnen een tijdreeks gedetecteerd.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten 

- [Azure Synapse Analytics-werk ruimte](../get-started-create-workspace.md) met een Azure data Lake Storage Gen2 opslag account geconfigureerd als de standaard opslag. U moet de Inzender voor *gegevens* van de opslag-blob van het data Lake Storage Gen2 bestands systeem waarmee u samenwerkt.
- Spark-pool in uw Azure Synapse Analytics-werkruimte. Zie [Een Spark-pool maken in Azure Synapse](../quickstart-create-sql-pool-studio.md) voor meer informatie.
- De stappen voorafgaand aan de configuratie die in de zelf studie worden beschreven, [configureren Cognitive Services in azure Synapse](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>Aan de slag
Importeer mmlspark-en configurate-service sleutels om aan de slag te gaan.

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 


cognitive_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", anomaly_key)

```

## <a name="text-analytics-sample"></a>Voor beeld van tekst analyse

De service [Text Analytics](../../cognitive-services/text-analytics/index.yml) biedt verschillende algoritmen voor het extraheren van intelligente inzichten uit tekst. We kunnen bijvoorbeeld het gevoel van een bepaald stuk ingevoerde tekst vinden. De service retourneert een score tussen 0,0 en 1,0, waarbij een lage score een negatief gevoel aangeeft en een hoge score een positief gevoel. In dit voorbeeld worden drie eenvoudige zinnen gebruikt en wordt het gevoel van elk ervan geretourneerd.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Verwachte resultaten

|tekst | gevoel|
|--|--|
| Ik heb het verkeer van deze spoed uur niet meer! | negatief |
| Dit is een hond | neutraal |
| I am so happy today, its sunny! (Ik ben zo blij vandaag, de zon schijnt!) | positief |

## <a name="computer-vision-sample"></a>Voor beeld van computer vision
[Computer Vision](../../cognitive-services/computer-vision/index.yml) analyseert afbeeldingen om structuur (zoals gezichten), objecten en beschrijvingen in natuurlijke taal te herkennen. In dit voor beeld wordt de volgende afbeelding labelen. Tags zijn omschrijvingen van één woord van dingen in de afbeelding, zoals herkenbare voorwerpen, personen, taferelen en acties.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Verwachte resultaten

|image | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [opritten, persoon, man, buiten, afzetten, sport, skateboard, Young, bord, shirt, lucht, Park, jongen, zijkant, springen, helling, slag, uitvoeren, in de vaart] |

## <a name="bing-image-search-sample"></a>Zoek voorbeeld van Bing image
[Bing Image Search](../../cognitive-services/bing-image-search/overview.md) doorzoekt het web om afbeeldingen op te halen die zijn gerelateerd aan de query in natuurlijke taal van een gebruiker. In dit voorbeeld gebruiken we een tekstquery die naar afbeeldingen met citaten zoekt. Er wordt een lijst met afbeeldings-URL's geretourneerd die foto's bevatten waarop de query betrekking heeft.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Verwachte resultaten

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Afwijkend detector-voor beeld

[Anomaly Detector](../../cognitive-services/anomaly-detector/index.yml) is geschikt voor het detecteren van onregelmatigheden in uw tijdreeksen. In dit voorbeeld gebruiken we de service om afwijkingen in de gehele tijdreeks te zoeken.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Verwachte resultaten

|tijdstempel | waarde | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826,0|onjuist|
|1972-02-01T00:00:00Z|799,0|onjuist|
|1972-03-01T00:00:00Z|890,0|onjuist|
|1972-04-01T00:00:00Z|900,0|onjuist|
|1972-05-01T00:00:00Z|766,0|onjuist|
|1972-06-01T00:00:00Z|805,0|onjuist|
|1972-07-01T00:00:00Z|821,0|onjuist|
|1972-08-01T00:00:00Z|20000,0|true|
|1972-09-01T00:00:00Z|883,0|onjuist|
|1972-10-01T00:00:00Z|898,0|onjuist|
|1972-11-01T00:00:00Z|957,0|onjuist|
|1972-12-01T00:00:00Z|924,0|onjuist|
|1973-01-01T00:00:00Z|881,0|onjuist|
|1973-02-01T00:00:00Z|837,0|onjuist|
|1973-03-01T00:00:00Z|9000,0|true|

## <a name="next-steps"></a>Volgende stappen

* [Voor beelden van Synapse-voorbeeld notitieblokken bekijken](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [MMLSpark GitHub opslag plaats](https://github.com/Azure/mmlspark)