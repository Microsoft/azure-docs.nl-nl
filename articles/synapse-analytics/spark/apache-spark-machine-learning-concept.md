---
title: Machine Learning met Apache Spark
description: Dit artikel bevat een conceptueel overzicht van de mogelijkheden voor machine learning en data technologie die beschikbaar zijn via Apache Spark in azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116922"
---
# <a name="machine-learning-with-apache-spark"></a>Machine learning met Apache Spark

Met Apache Spark in azure Synapse Analytics kunt u machine learning met big data, waardoor u waardevolle inzicht kunt krijgen in grote hoeveel heden gestructureerde, ongestructureerde en snel te verplaatsen gegevens. 

Deze sectie bevat een overzicht en zelf studies voor machine learning werk stromen, waaronder experimentele gegevens analyse, functie techniek, model training, model scores en implementatie.  

## <a name="synapse-runtime"></a>Synapse-runtime 
De Synapse-runtime is een gecuratorde omgeving die is geoptimaliseerd voor data Science en machine learning. De Synapse-runtime biedt standaard een aantal populaire open-source bibliotheken en bouwt samen in de Azure Machine Learning SDK. De Synapse-runtime omvat ook veel externe bibliotheken, waaronder PyTorch, Scikit-learn, XGBoost en meer.

Meer informatie over de beschik bare bibliotheken en gerelateerde versies door de gepubliceerde [Azure Synapse Analytics-runtime](../spark/apache-spark-version-support.md)weer te geven.

## <a name="exploratory-data-analysis"></a>Experimentele gegevens analyse
Wanneer u Apache Spark in azure Synapse Analytics gebruikt, zijn er verschillende ingebouwde opties om u te helpen uw gegevens te visualiseren, met inbegrip van Synapse-notitieblok diagram opties, toegang tot populaire open-source bibliotheken zoals Seaborn en matplotlib, en integratie met Synapse SQL en Power BI.

Meer informatie over de opties voor gegevens visualisatie en gegevens analyse kunt u vinden in het artikel over het [visualiseren van gegevens met behulp van Azure Synapse-notebooks](../spark/apache-spark-data-visualization.md).

## <a name="feature-engineering"></a>Functie-engineering
De Synapse-runtime bevat standaard een set bibliotheken die vaak worden gebruikt voor functie techniek. Voor grote gegevens sets kunt u Spark SQL, MLlib en Koalas gebruiken voor functie techniek. Voor kleinere gegevens sets bieden bibliotheken van derden, zoals numpy, Pandas en Scikit, ook nuttige methoden voor deze scenario's.

## <a name="train-models"></a>Modellen trainen
Er zijn verschillende opties voor het trainen van machine learning modellen met behulp van Azure Spark in azure Synapse Analytics: Apache Spark MLlib, Azure Machine Learning en verschillende andere open-source-bibliotheken. 

Raadpleeg het artikel over het [trainen van modellen in azure Synapse Analytics](../spark/apache-spark-machine-learning-training.md)voor meer informatie over de mogelijkheden van machine learning.

### <a name="sparkml-and-mllib"></a>SparkML en MLlib
Met de mogelijkheden van de gedistribueerde reken kracht van Spark in het geheugen is het een goede keuze voor de iteratieve algoritmen die worden gebruikt in machine learning-en grafiek berekeningen. ```spark.ml``` biedt een uniforme set Api's op hoog niveau waarmee gebruikers machine learning pijp lijnen kunnen maken en afstemmen. ```spark.ml```Ga naar de [Apache Spark ml-programmeer gids](https://spark.apache.org/docs/1.2.2/ml-guide.html)voor meer informatie over.

### <a name="azure-machine-learning-automated-ml"></a>Azure Machine Learning automatische ML
[Azure machine learning automaten](../../machine-learning/concept-automated-ml.md) (geautomatiseerd machine learning) helpt u bij het automatiseren van het proces voor het ontwikkelen van machine learning modellen. Zo kunnen gegevens wetenschappers, analisten en ontwikkel aars ML-modellen bouwen met een hoge schaal, efficiëntie en productiviteit, terwijl de kwaliteit van het model goed wordt. De onderdelen voor het uitvoeren van de Azure Machine Learning Automated ML SDK worden rechtstreeks in de Synapse-runtime gebouwd.

### <a name="open-source-libraries"></a>Open-source-bibliotheken
Elke Apache Spark pool in azure Synapse Analytics wordt geleverd met een set vooraf geladen en populaire machine learning bibliotheken.  Enkele van de relevante machine learning bibliotheken die standaard zijn opgenomen, zijn:

- [Scikit-Learn](https://scikit-learn.org/stable/index.html) is een van de populairste machine learning bibliotheken met één knoop punt voor klassieke ml-algoritmen. Scikit-Learn ondersteunt de meeste van de bewaakte en niet-bewaakte leer algoritmen, en kan ook worden gebruikt voor gegevens analyse en het analyseren van gegevens.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) is een populaire machine learning bibliotheek met geoptimaliseerde algoritmen voor trainings beslissings structuren en wille keurige forests. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensor flow](https://www.tensorflow.org/) zijn krachtige python-bibliotheken met diepe lessen. Binnen een Apache Spark pool in azure Synapse Analytics kunt u deze bibliotheken gebruiken om modellen voor één machine te bouwen door het aantal uitvoerende machines in uw pool in te stellen op nul. Hoewel Apache Spark niet functioneel is in deze configuratie, is het een eenvoudige en rendabele manier om modellen voor één machine te maken.

## <a name="track-model-development"></a>Ontwikkeling van traceringsmodellen
[MLFlow](https://www.mlflow.org/) is een open-source bibliotheek voor het beheren van de levens cyclus van uw machine learning experimenten. MLFlow tracking is een onderdeel van MLflow dat de metrische gegevens van uw training en model artefacten registreert en registreert. Ga naar deze zelf studie over het [gebruik van MLFlow](../../machine-learning/how-to-use-mlflow.md)voor meer informatie over hoe u MLFlow tracking kunt gebruiken via Azure Synapse Analytics en Azure machine learning.

## <a name="model-scoring"></a>Model Score
Model scores of detraining is de fase waarin een model wordt gebruikt voor het maken van voor spellingen. Voor een model Score met SparkML of MLLib kunt u gebruikmaken van de systeem eigen Spark-methoden om het dezicht rechtstreeks op een Spark-data frame uit te voeren. Voor andere open source-bibliotheken en-model typen kunt u ook een Spark UDF maken om de deinterferentie van grote gegevens sets te verruimen. Voor kleinere gegevens sets kunt u ook gebruikmaken van de methoden van het systeem eigen model voor invallen van de bibliotheek.

## <a name="register-and-serve-models"></a>Modellen registreren en gebruiken
Als u een model registreert, kunt u meta gegevens over modellen in uw werk ruimte opslaan, versie en bijhouden. Nadat u klaar bent met het trainen van uw model, kunt u uw model registreren bij het [REGI ster van het Azure machine learning model](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere). Na registratie kunnen ONNX-modellen ook worden gebruikt voor [het verrijken van de gegevens](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) die zijn opgeslagen in toegewezen SQL-groepen.

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende zelf studies om aan de slag te gaan met machine learning in azure Synapse Analytics:
- [Gegevens analyseren met Azure Synapse-notebooks](../spark/apache-spark-data-visualization-tutorial.md)

- [Een machine learning model trainen met automatische MILLILITERs](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Een machine learning model trainen met Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)
