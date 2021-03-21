---
title: machine learning modellen trainen met Apache Spark
description: Apache Spark in azure Synapse Analytics gebruiken om machine learning modellen te trainen
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98116803"
---
# <a name="train-machine-learning-models"></a>Machine learning-modellen trainen
Met Apache Spark in azure Synapse Analytics kunt u machine learning met big data, waardoor u waardevolle inzicht kunt krijgen in grote hoeveel heden gestructureerde, ongestructureerde en snel te verplaatsen gegevens. Er zijn verschillende opties voor het trainen van machine learning modellen met behulp van Azure Spark in azure Synapse Analytics: Apache Spark MLlib, Azure Machine Learning en verschillende andere open-source-bibliotheken. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML en MLlib
Apache Spark in Azure Synapse Analytics is een van de implementaties van Apache Spark van Microsoft in de cloud. Het biedt een uniform, open-source, parallel gegevens verwerkings raamwerk dat in-Memory verwerking ondersteunt om big data Analytics te stimuleren. De Spark-verwerkings engine is gebouwd voor snelheid, gebruiks gemak en geavanceerde analyses. Met de mogelijkheden van de gedistribueerde reken kracht van Spark in het geheugen is het een goede keuze voor de iteratieve algoritmen die worden gebruikt in machine learning-en grafiek berekeningen. 

Er zijn twee schaal bare machine learning bibliotheken die algoritmen model leren met deze gedistribueerde omgeving: MLlib en SparkML. MLlib bevat de oorspronkelijke API die boven op Rdd's is gebouwd. SparkML is een nieuwer pakket dat een op een hoger niveau ingebouwde API biedt voor het bouwen van ML-pijp lijnen. SparkML biedt nog geen ondersteuning voor alle functies van MLlib, maar vervangt MLlib als de standaard machine learning-bibliotheek van Spark.

> [!NOTE]
> 
> U kunt meer te weten komen over het maken van een SparkML-model door deze [zelf studie](../spark/apache-spark-azure-machine-learning-tutorial.md)te volgen.

## <a name="popular-libraries"></a>Populaire bibliotheken
Elke Apache Spark pool in azure Synapse Analytics wordt geleverd met een set vooraf geladen en populaire machine learning bibliotheken. Deze bibliotheken bieden herbruikbare code die u mogelijk wilt toevoegen aan uw Program ma's of projecten. Enkele van de relevante machine learning bibliotheken die standaard zijn opgenomen, zijn:
- [Scikit-Learn](https://scikit-learn.org/stable/index.html) is een van de populairste machine learning bibliotheken met één knoop punt voor klassieke ml-algoritmen. Scikit-Learn ondersteunt de meeste van de bewaakte en niet-bewaakte leer algoritmen, en kan ook worden gebruikt voor gegevens analyse en het analyseren van gegevens.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) is een populaire machine learning bibliotheek met geoptimaliseerde algoritmen voor trainings beslissings structuren en wille keurige forests. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensor flow](https://www.tensorflow.org/) zijn krachtige python-bibliotheken met diepe lessen. Binnen een Apache Spark pool in azure Synapse Analytics kunt u deze bibliotheken gebruiken om modellen voor één machine te bouwen door het aantal uitvoerende machines in uw pool in te stellen op nul. Hoewel Apache Spark niet functioneel is in deze configuratie, is het een eenvoudige en rendabele manier om modellen voor één machine te maken.

U kunt meer te weten komen over de beschik bare bibliotheken en gerelateerde versies door de gepubliceerde [Azure Synapse Analytics-runtime](../spark/apache-spark-version-support.md)weer te geven.

## <a name="mmlspark"></a>MMLSpark
De micro Soft Machine Learning-bibliotheek voor Apache Spark is [MMLSpark](https://github.com/Azure/mmlspark). Deze bibliotheek is ontworpen om gegevens wetenschappers productiever te maken op Spark, de frequentie van experimenten te verhogen en de geavanceerde machine learning technieken uit te voeren, waaronder diepe kennis, op grote gegevens sets. 

MMLSpark biedt een laag bovenop de Api's met een laag niveau van SparkML bij het bouwen van schaal bare ML-modellen, zoals het indexeren van teken reeksen, het afdwingen van gegevens in een indeling die wordt verwacht door machine learning algoritmen en het samen stellen van functie vectoren. De MMLSpark-bibliotheek vereenvoudigt deze en andere veelvoorkomende taken voor het bouwen van modellen in PySpark.

## <a name="automated-ml-in-azure-machine-learning"></a>Automatische ML in Azure Machine Learning 
Azure Machine Learning is een cloudomgeving die u kunt gebruiken voor het trainen, implementeren, automatiseren, beheren en volgen van machine learning-modellen. Automatische ML in Azure Machine Learning accepteert trainings gegevens en configuratie-instellingen en herhaalt automatisch combi Naties van verschillende methoden voor het normaliseren/standaardiseren van functies, modellen en afstemming-instellingen om het beste model te ontvangen. 

Bij gebruik van automatische ML in azure Synapse Analytics kunt u gebruikmaken van de diepe integratie tussen de verschillende services om de verificatie & model training te vereenvoudigen. 

> [!NOTE]
> 
> U vindt meer informatie over het maken van een Azure Machine Learning-experiment voor automatische ML door deze [zelf studie](./spark/../apache-spark-azure-machine-learning-tutorial.md)te volgen.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) biedt machine learning mogelijkheden om algemene problemen op te lossen, zoals het analyseren van tekst voor emotioneel sentiment of het analyseren van installatie kopieën om objecten of gezichten te herkennen. U hebt geen speciale machine learning- of data science-kennis nodig om deze services te kunnen gebruiken. Een cognitieve service biedt deel of alle onderdelen in een machine learning oplossing: gegevens, algoritme en getraind model. Deze services zijn bedoeld om algemene kennis van uw gegevens te vereisen zonder dat u ervaring hoeft te hebben met machine learning of gegevens wetenschap. U kunt deze vooraf getrainde Cognitive Services automatisch gebruiken in azure Synapse Analytics.

## <a name="next-steps"></a>Volgende stappen
In dit artikel vindt u een overzicht van de verschillende opties voor het trainen van machine learning modellen binnen Apache Spark Pools in azure Synapse Analytics. U kunt meer te weten komen over model training door onderstaande zelf studie te volgen:

- Voer automatische ML experimenten uit met behulp van Azure Machine Learning en Azure Synapse Analytics: [zelf studie over automatisch ml](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- SparkML experimenten uitvoeren: [Apache SparkML-zelf studie](../spark/apache-spark-machine-learning-mllib-notebook.md)
- De standaard bibliotheken weer geven: [Azure Synapse Analytics runtime](../spark/apache-spark-version-support.md)