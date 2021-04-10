---
title: Overzicht van machine learning-Azure HDInsight
description: Overzicht van big data machine learning opties voor clusters in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 492663ca35ed04da09e7f0d198444a4d40178e2b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867522"
---
# <a name="machine-learning-on-hdinsight"></a>Machine learning op HDInsight

HDInsight maakt het mogelijk om machine learning met big data, waardoor u waardevolle inzichten kunt verkrijgen van grote hoeveel heden (PETA bytes of zelfs Exabyte) van gestructureerde, ongestructureerde en snel bewegende gegevens. Er zijn verschillende machine learning opties in HDInsight: SparkML en Apache Spark MLlib, R, Apache Hive en de Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML en MLlib

[HDInsight Spark](spark/apache-spark-overview.md) is een door Azure gehoste aanbieding van [Apache Spark](https://spark.apache.org/), een gecombineerd, open source, parallel data processing framework dat ondersteuning biedt voor in-Memory verwerking om Big Data Analytics te stimuleren. De Spark-verwerkings engine is gebouwd voor snelheid, gebruiks gemak en geavanceerde analyses. Met de mogelijkheden van de gedistribueerde reken kracht van Spark in het geheugen is het een goede keuze voor de iteratieve algoritmen die worden gebruikt in machine learning-en grafiek berekeningen. Er zijn twee schaal bare machine learning bibliotheken die algoritmen model leren met deze gedistribueerde omgeving: MLlib en SparkML. MLlib bevat de oorspronkelijke API die boven op Rdd's is gebouwd. SparkML is een nieuwer pakket dat een op een hoger niveau ingebouwde API biedt voor het bouwen van ML-pijp lijnen. SparkML biedt nog geen ondersteuning voor alle functies van MLlib, maar vervangt MLlib als de standaard machine learning-bibliotheek van Spark.

De micro Soft Machine Learning-bibliotheek voor Apache Spark is [MMLSpark](https://github.com/Azure/mmlspark). Deze bibliotheek is ontworpen om gegevens wetenschappers productiever te maken op Spark, de frequentie van experimenten te verhogen en de geavanceerde machine learning technieken uit te voeren, waaronder diepe kennis, op zeer grote gegevens sets. MMLSpark biedt een laag bovenop de Api's met een laag niveau van SparkML bij het bouwen van schaal bare ML-modellen, zoals het indexeren van teken reeksen, het afdwingen van gegevens in een indeling die wordt verwacht door machine learning algoritmen en het samen stellen van functie vectoren. De MMLSpark-bibliotheek vereenvoudigt deze en andere veelvoorkomende taken voor het bouwen van modellen in PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) is momenteel de meest populaire statistische programmeer taal in de wereld. Het is een open-source hulp programma voor gegevens visualisatie met een community van meer dan 2.500.000 gebruikers en groei. Met zijn bloeiende gebruikers basis en meer dan 8.000 bijgedragen pakketten, is R een waarschijnlijke keuze voor veel bedrijven die machine learning nodig hebben. U kunt een HDInsight-cluster maken met ML services die klaar zijn voor gebruik met enorme gegevens sets en modellen. Deze mogelijkheid biedt gegevens wetenschappers en statistici met een vertrouwde R-interface die op aanvraag kan worden geschaald via HDInsight, zonder de overhead van het instellen en onderhouden van het cluster.

:::image type="content" source="./media/hdinsight-machine-learning-overview/training-for-prediction.png" alt-text="Training voor voor spellingen met R server" border="false":::

Het edge-knooppunt van een cluster biedt een handige plaats om verbinding te maken met het cluster en om uw R-scripts uit te voeren.  U kunt ook R-scripts uitvoeren op de knoop punten van het cluster met behulp van de Hadoop-toewijzing van de Scale-map of door middel van Spark Compute-contexten.

Met ML Services op HDInsight met Spark kunt u training over de knoop punten van een cluster parallelliseren met behulp van een Spark-Compute-context. U kunt R-scripts rechtstreeks op het Edge-knoop punt uitvoeren, waarbij alle beschik bare kernen parallel, indien nodig, worden gebruikt. U kunt de code ook uitvoeren vanuit het Edge-knoop punt om de verwerking te starten die wordt gedistribueerd over alle knoop punten in het cluster. Met Spark-Services in HDInsight kunnen ook gelijktijdig-functies van open-source R-pakketten, indien gewenst, worden ingeschakeld.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning en Apache Hive

Azure Machine Learning voorziet in hulpprogram ma's om predictive analytics te model leren en een volledig beheerde service die u kunt gebruiken om uw voorspellende modellen te implementeren als kant-en-klare webservices. Azure Machine Learning is een volledige predictive analytics-oplossing in de cloud die u kunt gebruiken voor het maken, testen, operationeel maken en beheren van voorspellende modellen. Maak een keuze uit een grote algoritme bibliotheek, gebruik een webgebaseerde Studio voor het ontwikkelen van modellen en implementeer uw model eenvoudig als een webservice.

:::image type="content" source="./media/hdinsight-machine-learning-overview/azure-machine-learning.png" alt-text="Overzicht van Microsoft Azure machine learning" border="false":::

Maak functies voor gegevens in een HDInsight Hadoop-cluster met behulp van [Hive-query's](../machine-learning/team-data-science-process/create-features-hive.md). *Feature engineering* probeert de voorspellende kracht van leer algoritmen te verhogen door functies te maken op basis van onbewerkte gegevens die het leer proces vergemakkelijken. U kunt HiveQL-query's uitvoeren vanuit Azure Machine Learning Studio (klassiek) en toegang krijgen tot gegevens die in Hive zijn verwerkt en worden opgeslagen in Blob Storage met behulp van de [module gegevens importeren](../machine-learning/classic/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Grondige kennis](https://www.microsoft.com/en-us/research/group/dltc/) is een vertakking van machine learning die gebruikmaakt van Neural-netwerken, die zijn geïnspireerd op de biologische processen van het menselijke hersenen. Veel onderzoekers zien een diep gaande leer proces als een toezeggings benadering voor het verbeteren van kunst matige intelligentie. Voor beelden van diep gaande lessen zijn gesp roken taal vertalers, afbeeldings herkennings systemen en de reden van de computer.

Micro soft heeft de gratis, gebruiks vriendelijke, open-source [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)ontwikkeld om haar eigen werk in de hand te zetten. Deze Toolkit wordt gebruikt door een groot aantal micro soft-producten, van bedrijven wereld wijd, met een nood zaak om diep gaande lessen op schaal te implementeren en door studenten die geïnteresseerd zijn in de nieuwste algoritmen en technieken.

## <a name="see-also"></a>Zie ook

### <a name="scenarios"></a>Scenario's

* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Aanbevelingen voor de film genereren met Apache mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive en Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive en Azure Machine Learning end-to-end](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine learning met Apache Spark op HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Uitgebreide trainings bronnen

* [Microsoft Cognitive Toolkit diepe leer model gebruiken met Azure HDInsight Spark cluster](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Diep gaande lessen en AI-Frameworks op de Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
