---
title: Cognitive Services voor Big Data
description: Meer informatie over het gebruik van Azure Cognitive Services voor grote gegevenssets met behulp van Python, Java en Scala. Met Cognitive Services voor Big data kunt u intelligente modellen rechtstreeks in Apache Spark&trade; en SQL-berekeningen opnemen.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: aaade03edbbb109656fb7371a063cdc2512c5a20
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461616"
---
# <a name="azure-cognitive-services-for-big-data"></a>Azure Cognitive Services voor Big Data

![Azure Cognitive Services voor Big Data](media/cognitive-services-big-data-overview.svg)

Met Azure Cognitive Services voor Big Data kunnen gebruikers terabytes aan gegevens doorvoeren via Cognitive Services met behulp van [Apache Spark&trade;](/dotnet/spark/what-is-spark). Met de Cognitive Services voor Big Data kunt u eenvoudig intelligente toepassingen op grote schaal maken met elke gegevensopslag.

Met Cognitive Services voor Big data kunt u intelligente modellen rechtstreeks in Apache Spark&trade; en SQL-berekeningen opnemen. Dankzij hulpprogramma's moeten ontwikkelaars zich niet meer bezighouden met netwerkdetails op laag niveau, zodat ze zich kunnen richten op het maken van slimme, gedistribueerde toepassingen.

## <a name="features-and-benefits"></a>Functies en -voordelen

Cognitive Services voor Big Data kunnen gebruikmaken van services uit elke regio in de wereld, alsook [in een container geplaatste Cognitive Services](../cognitive-services-container-support.md). Containers ondersteunen weinig of geen connectiviteitsimplementaties met ultralage latentiereacties. In een container geplaatste Cognitive Services kunnen lokaal worden uitgevoerd, rechtstreeks op de werkknooppunten van uw Spark-cluster of op een externe orchestrator zoals Kubernetes.

## <a name="supported-services"></a>Ondersteunde services

[Cognitive Services](../index.yml), die toegankelijk zijn via API's en SDK's, helpen ontwikkelaars intelligente toepassingen bouwen zonder vaardigheden op het gebied van AI of gegevenswetenschap. Met Cognitie Services kunnen uw toepassingen zien, horen, spreken, begrijpen en nadenken. Om Cognitive Services te gebruiken moet uw toepassing gegevens versturen naar de service via het netwerk. Zodra deze ontvangen zijn, stuurt de service een intelligent antwoord terug. De volgende services zijn beschikbaar voor big data-workloads:

### <a name="vision"></a>Vision

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Computer Vision](../computer-vision/index.yml "Computer Vision")| De Computer Vision-service geeft u toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retourneren van informatie. |
|[Face](../face/index.yml "Face")| De Face-service biedt toegang tot geavanceerde gezichtsalgoritmen, waardoor detectie en herkenning van gezichtskenmerken mogelijk worden. |

### <a name="speech"></a>Speech

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Speech Service](../speech-service/index.yml "Speech Service")|De Speech-service biedt toegang tot functies zoals spraakherkenning, spraaksynthese, spraakomzetting en verificatie en identificatie van de spreker.|

### <a name="decision"></a>Besluit

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Anomaliedetectie](../anomaly-detector/index.yml "Anomaly Detector") | Met de anomaliedetectieservice (preview) kunt u afwijkingen in uw tijdreeksgegevens controleren en detecteren.|

### <a name="language"></a>Taal

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Tekstanalyse](../text-analytics/index.yml "Tekstanalyse")| De Text Analytics-servicee biedt natuurlijke taalverwerking via onbewerkte tekst voor sentimentanalyse, sleuteltermextractie en taaldetectie.|

### <a name="search"></a>Search

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Bing Afbeeldingen zoeken](/azure/cognitive-services/bing-image-search "Bing Afbeeldingen zoeken")|De Bing Image Search-service retourneert een weergave van afbeeldingen die relevant zijn voor de query van de gebruiker.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Ondersteunde programmeertalen voor Cognitive Services voor Big Data

De Cognitive Services voor Big Data zijn gebaseerd op Apache Spark. Apache Spark is een gedistribueerde rekenbibliotheek die ondersteuning biedt voor Java, Scala, Python, R en vele andere talen. Deze talen worden momenteel ondersteund.

### <a name="python"></a>Python

We bieden een PySpark-API in de `mmlspark.cognitive`-naamruimte van [Microsoft ML voor Apache Spark](https://aka.ms/spark). Raadpleeg het [Python Developer API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html) voor meer informatie. Zie de [Python-voorbeelden](samples-python.md) voor gebruiksvoorbeelden.

### <a name="scala-and-java"></a>Scala en Java

We bieden een op Scala- en op Java-gebaseerde Spark-API in de `com.microsoft.ml.spark.cognitive`-naamruimte van [Microsoft ML voor Apache Spark](https://aka.ms/spark). Raadpleeg het [Scala Developer API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package) voor meer informatie. Zie de [Scala-voorbeelden](samples-scala.md) voor gebruiksvoorbeelden.

## <a name="supported-platforms-and-connectors"></a>Ondersteunde platforms en connectors

De Cognitive Services voor Big Data vereisen Apache Spark. Er zijn verschillende Apache Spark-platforms die ondersteuning bieden voor de Cognitive Services voor Big Data.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) is een op Apache Spark gebaseerd analyseplatform, geoptimaliseerd voor het Microsoft Azure-platform voor cloudservices. Het biedt installatie met één klik, gestroomlijnde werkstromen en een interactieve werkruimte waarmee gegevenswetenschappers, gegevenstechnici en bedrijfsanalisten samen kunnen werken.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) is een zakelijke datawarehouse die parallelle verwerking op grote schaal gebruikt. Met Synapse Analytics kunt u snel complexe query's uitvoeren op petabytes aan gegevens. Azure Synapse Analytics biedt beheerde Spark Pools om Spark-taken uit te voeren met een intuïtieve Jupyter Notebook-interface.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Azure Kubernetes Service (AKS)](../../aks/index.yml) organiseert Docker-containers en gedistribueerde toepassingen op grote schaal. AKS is een beheerde Kubernetes-aanbieding die het gebruik van Kubernetes in Azure vereenvoudigt. Met Kubernetes kunt u de schaal, latentie en netwerken van Cognitive Service nauwkeurig controleren. We raden u echter aan Azure Databricks of Azure Synapse Analytics te gebruiken als u niet bekend bent met Apache Spark.

### <a name="data-connectors"></a>Gegevensconnectors

Zodra u een Spark-cluster hebt, is de volgende stap om verbinding te maken met uw gegevens. Apache Spark heeft een brede verzameling database-connectors. Met deze connectors kunnen toepassingen werken met grote gegevenssets, ongeacht waar ze zijn opgeslagen. Zie de [lijst met ondersteunde gegevensbronnen voor Azure Databricks](/azure/databricks/data/data-sources/) voor meer informatie over ondersteunde databases en connectors.

## <a name="concepts"></a>Concepten

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/) is een geïntegreerde analyse-engine voor gegevensverwerking op grote schaal. Het framework voor parallelle verwerking boost de prestaties van big data en analysetoepassingen. Spark kan worden gebruikt als een systeem voor batch-en stroomverwerking, zonder dat de basiscode van de toepassing veranderd moet worden.

Het DataFrame vormt de basis van Spark: een tabelverzameling van gegevens die zijn verdeeld over de Apache Spark-werkknooppunten. Een Spark DataFrame is zoals een tabel in een relationele database of aan een gegevensframe in R/Python, maar met onbegrensde schaal. DataFrames kunnen worden samengesteld uit verschillende bronnen, zoals: gestructureerde gegevensbestanden, tabellen in Hive of externe databases. Zodra uw gegevens zich in een Spark-dataframe bevinden, kunt u het volgende doen:

   - Berekeningen in SQL-stijl uitvoeren, zoals tabellen samenvoegen en filteren.
   - Functies toepassen op grote gegevenssets met een parallellisme in MapReduce-stijl.
   - Gedistribueerde machine learning toepassen met behulp van Microsoft Machine Learning voor Apache Spark.
   - Gebruik de Cognitive Services voor Big Data om uw gegevens te verrijken met kant-en-klare intelligente services.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning voor Apache Spark (MMLSpark)

[Microsoft Machine Learning voor Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) is een open-source, gedistribueerde machine learning-bibliotheek (ML) die gebouwd is op Apache Spark. De Cognitive Services voor Big Data maken deel uit van dit pakket. Daarnaast bevat MMLSpark verschillende andere ML-hulpprogramma's voor Apache Spark zoals LightGBM, Vowpal Wabbit, OpenCV, LIME en meer. Met MMLSpark kunt u krachtige voorspellende en analytische modellen bouwen vanuit elke Spark-gegevensbron.

### <a name="http-on-spark"></a>HTTP op Spark

Cognitive Services voor Big Data is een voorbeeld van hoe we intelligente webservices kunnen integreren met big data. Webservices ondersteunen tal van toepassingen wereldwijd, en de meeste services communiceren via het Hypertext Transfer Protocol (HTTP). Als u wilt werken met *willekeurige* webservices op grote schaal, dan bieden we HTTP op Spark. Met HTTP op Spark kunt u terabytes aan gegevens doorgeven via elke webservice. Achter de schermen gebruiken we deze technologie om Cognitive Services voor Big Data te ondersteunen.

## <a name="developer-samples"></a>Voorbeelden voor ontwikkelaars

- [Recept: Voorspellend onderhoud](recipes/anomaly-detection.md)
- [Recept: Verkenning van intelligente kunst](recipes/art-explorer.md)

## <a name="blog-posts"></a>Blogberichten

- [Meer informatie over hoe Cognitive Services werken op Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Sneeuwluipaarden redden met Deep Learning en Computer Vision op Spark](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research Podcast: MMLSpark, de basis van AI for Good met Mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [Academisch technisch document: Grootschalige intelligente microservices](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Webinars en video's

- [De Azure Cognitive Services op Spark: Clusters met ingesloten intelligente services](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Keynote van de Spark Summit: Schaalbare AI for Good](https://databricks.com/session_eu19/scalable-ai-for-good)
- [De Cognitive Services voor Big Data in Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Korte presentatie over grootschalige intelligente microservices](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Cognitive Services voor Big Data](getting-started.md)
- [Eenvoudige Python-voorbeelden](samples-python.md)
- [Eenvoudige Scala-voorbeelden](samples-scala.md)