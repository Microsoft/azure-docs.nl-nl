---
title: 'Recept: intelligent art verkennen met de Cognitive Services voor Big Data'
titleSuffix: Azure Cognitive Services
description: Dit recept laat zien hoe u een Doorzoek bare Art-data base maakt met behulp van Azure Search en MMLSpark.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363286"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Recept: intelligent art verkennen met de Cognitive Services voor Big Data

In dit voor beeld gebruiken we de Cognitive Services voor Big data om intelligente aantekeningen toe te voegen aan de Open-Access-verzameling vanuit het werk in de Republiek in het buitenste Museum (vervuld). Hierdoor kunnen we een intelligente zoek machine maken met behulp van Azure Search zelfs zonder hand matige aantekeningen. 

## <a name="prerequisites"></a>Vereisten

* U moet een abonnements sleutel hebben voor Computer Vision en Cognitive Search. Volg de instructies in [Create a cognitive Services account](../../cognitive-services-apis-create-account.md) om u te abonneren op computer vision en uw sleutel op te halen.
  > [!NOTE]
  > Zie [Azure Cognitive Search](https://azure.microsoft.com/services/search/#pricing)voor prijs informatie.

## <a name="import-libraries"></a>Bibliotheken importeren

Voer de volgende opdracht uit om bibliotheken voor dit recept te importeren.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Abonnements sleutels instellen

Voer de volgende opdracht uit om variabelen voor service sleutels in te stellen. Voeg uw abonnements sleutels in voor Computer Vision en Azure Cognitive Search.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>De gegevens lezen

Voer de volgende opdracht uit om gegevens te laden uit de Open Access-verzameling die aan de voor waarde is voldaan.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>De afbeeldingen analyseren

Voer de volgende opdracht uit om Computer Vision te gebruiken op de verzameling Open Access-illustraties van de VERVULDe toepassing. Als gevolg hiervan krijgt u visuele functies van de illustraties.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>De zoek index maken

Voer de volgende opdracht uit om de resultaten naar Azure Search te schrijven om een zoek machine van de illustraties te maken met verrijkte meta gegevens van Computer Vision.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Query's uitvoeren op de zoek index

Voer de volgende opdracht uit om een query uit te voeren op de Azure Search-index.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van [Cognitive Services voor Big data voor anomalie detectie](anomaly-detection.md).