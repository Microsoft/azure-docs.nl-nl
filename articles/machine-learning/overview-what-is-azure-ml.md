---
title: Wat is Azure Machine Learning?
description: Azure Machine Learning is een geïntegreerde Data Science-oplossing voor datawetenschappers en MLops om ML-toepassingen te modelleren en te implementeren op cloudschaal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 11/04/2020
ms.custom: devx-track-python
ms.openlocfilehash: a9d471e81c348bfaa92c5d15774517f0d9e0e43f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683354"
---
# <a name="what-is-azure-machine-learning"></a>Wat is Azure Machine Learning?

In dit artikel wordt aandacht besteed aan Azure Machine Learning, een cloudomgeving die u kunt gebruiken voor het trainen, implementeren, automatiseren, beheren en volgen van ML-modellen. 

Azure Machine Learning kan worden gebruikt voor elk type machine learning, van klassieke ML tot deep learning, onder supervisie en zonder supervisie. Ongeacht of u liever in Python of R schrijft met de SDK of opties voor geen of weinig code wilt gebruiken in [de studio](#build-ml-models-in-the-studio), kunt u in een werkruimte van Azure Machine Learning machine learning en deep learning-modellen bouwen, trainen en volgen. 

U kunt modellen eerst op uw lokale computer trainen en vervolgens opschalen naar de cloud. 

De service is ook compatibel met populaire opensource-programma's voor deep learning en reinforcement learning zoals PyTorch, TensorFlow, scikit-learn en Ray RLlib. 

> [!Tip]
> **Gratis proefversie**  Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree). U ontvangt tegoed wat u kunt uitgeven aan Azure-services. Als uw tegoed op is, kunt u het account behouden en de [gratis Azure-services](https://azure.microsoft.com/free/) gebruiken. Er worden nooit kosten in rekening gebracht bij uw creditcard tenzij u de instellingen expliciet wijzigt en aangeeft dat u wilt betalen.


## <a name="what-is-machine-learning"></a>Wat is machine learning?

Machine learning is een techniek van gegevenswetenschap die computers in staat stelt om op basis van bestaande gegevens, toekomstig gedrag, resultaten en trends te voorspellen. Met behulp van machine learning kunnen computers leren zonder dat ze expliciet worden geprogrammeerd.

Prognoses of voorspellingen op basis van machine learning kunnen apps en apparaten slimmer maken. Wanneer u bijvoorbeeld online winkelt, helpt machine learning op basis van wat u eerder hebt gekocht, bij het aanraden van andere producten waarvoor u mogelijk belangstelling hebt. Of wanneer uw creditcard wordt gebruikt, vergelijkt machine learning de transactie met een database van transacties om fraude te detecteren. En wanneer uw robotstofzuiger een kamer stofzuigt, helpt machine learning bij het besluiten wanneer de taak klaar is.

## <a name="machine-learning-tools-to-fit-each-task"></a>Machine learning-hulpprogramma's voor elke taak 

Azure Machine Learning biedt alle hulpprogramma's die ontwikkelaars en gegevenswetenschappers nodig hebben voor hun machine learning-werkstromen, waaronder:
+ De [Azure Machine Learning-ontwerpfunctie](tutorial-designer-automobile-price-train-score.md): modules slepen om experimenten te bouwen en vervolgens pijplijnen te implementeren.

+ Jupyter-notebooks: gebruik onze [voorbeelden van notebooks](https://github.com/Azure/MachineLearningNotebooks) of maak uw eigen notebooks om onze <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK voor Python</a> in te zetten voor uw machine learning. 

+ R-scripts of notebooks waarin u de <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK voor R</a> gebruikt voor het schrijven van uw eigen code of het gebruik van R-modules in de ontwerpfunctie.

+ De [Many Models Solution Accelerator](https://aka.ms/many-models) (preview) heeft Azure Machine Learning als basis en stelt u in staat om honderden, of zelfs duizenden machine Learning-modellen, te trainen, te gebruiken en te beheren.

+ [Machine Learning-extensie voor Visual Studio Code-gebruikers](tutorial-setup-vscode-extension.md)

+ [Machine learning-CLI](reference-azure-machine-learning-cli.md)

+ Opensource-frameworks zoals PyTorch, TensorFlow, scikit-learn en nog veel meer

+ [Reinforcement learning](how-to-use-reinforcement-learning.md) met Ray RLlib

U kunt zelfs [MLflow gebruiken om metrische gegevens bij te houden en modellen te implementeren](how-to-use-mlflow.md) of Kubeflow voor het [bouwen van end-to-end werkstroompijplijnen](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>ML-modellen bouwen in Python of R

Begin met het trainen van modellen op uw lokale machine met behulp van de <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python-SDK</a> of <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R-SDK</a> van Azure Machine Learning. Vervolgens kunt u uitschalen naar de cloud. 

Dankzij de vele beschikbare [rekendoelen](how-to-create-attach-compute-studio.md) zoals Azure Machine Learning Compute en [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), en [geavanceerde afstemmingsservices van hyperparameters](how-to-tune-hyperparameters.md), kunt u sneller betere modellen bouwen, met de kracht van de cloud.

U kunt de SDK ook gebruiken voor het [automatiseren van het trainen en afstemmen van modellen](tutorial-auto-train-models.md).

## <a name="build-ml-models-in-the-studio"></a>ML-modellen bouwen in de studio

[Azure Machine Learning Studio](https://studio.azureml.net) is een webportal in Azure Machine Learning voor opties met weinig code en zonder code voor het trainen van modellen, implementatie en asset-management. De studio kan worden geïntegreerd met de Azure Machine Learning SDK voor een naadloze ervaring. Zie [Wat is Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) voor meer informatie.

+ **Azure Machine Learning-ontwerpprogramma**

  Gebruik [de ontwerpfunctie](concept-designer.md) om Machine Learning-modellen te trainen en implementeren zonder code te schrijven. Volg de [zelfstudie over ontwerpen](tutorial-designer-automobile-price-train-score.md) om aan de slag te gaan. 

  ![Bewegende GIF van de interface voor slepen en neerzetten in Azure Machine Learning Designer](media/concept-designer/designer-drag-and-drop.gif)

+ **Experimenten bijhouden**

  Meer informatie over het [bijhouden en visualiseren van experimenten voor datatechnologie](tutorial-first-experiment-automated-ml.md) in de studio. 

    ![Details van de uitvoering in Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)


+ **En nog veel meer...**

  Ga naar Azure Machine Learning Studio op [ml.azure.com](https://studio.azureml.net). 


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Implementatie- en levenscyclusbeheer
Wanneer u het juiste model hebt, kunt u dit eenvoudig gebruiken in een webservice, op een IoT-apparaat of in Power BI. Zie het artikel [Hoe implementeren en waar](how-to-deploy-and-where.md) voor meer informatie.

Daarna kunt u uw geïmplementeerde modellen beheren met de [Azure Machine Learning SDK voor Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py), [Azure Machine Learning Studio](https://ml.azure.com) of de [machine learning CLI](reference-azure-machine-learning-cli.md).

Deze modellen kunnen in [realtime](how-to-consume-web-service.md) of [asynchroon](./tutorial-pipeline-batch-scoring-classification.md) worden uitgevoerd om voorspellingen te retourneren voor grote hoeveelheden gegevens.

En met geavanceerde [machine learning-pijplijnen](concept-ml-pipelines.md) kunt u samenwerken aan alle stappen, van de gegevensvoorbereiding, modeltraining en evaluatie tot de implementatie. Met pijplijnen kunt u het volgende doen:

* Het end-to-end-machine learning-proces automatiseren in de cloud
* Onderdelen opnieuw gebruiken en stappen alleen opnieuw uitvoeren als dat nodig is
* In elke stap verschillende rekenresources gebruiken
* Taken voor scoren in batch uitvoeren

Als u scripts wilt gebruiken om uw machine learning-werkstroom te automatiseren, biedt de [machine learning-CLI](reference-azure-machine-learning-cli.md) opdrachtregelprogramma's voor het uitvoeren van algemene taken, zoals het verzenden van een trainingsrun of het implementeren van een model.

Zie [Volgende stappen](#next-steps) om aan de slag te gaan met Azure Machine Learning.

## <a name="integration-with-other-services"></a>Integratie met andere services

Azure Machine Learning werkt met andere services op het Azure-platform en kan ook worden geïntegreerd met opensource-tools zoals Git en MLFlow.

+ Rekendoelen zoals __Azure Kubernetes Service__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__ en __Azure HDInsight__. Zie [Wat zijn rekendoelen?](concept-compute-target.md) voor meer informatie over rekendoelen.
+ __Azure Event Grid__. Zie [Consume Azure Machine Learning events](./how-to-use-event-grid.md) (Azure Machine Learning-gebeurtenissen gebruiken) voor meer informatie.
+ __Azure Monitor__. Zie [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md) (Azure Machine Learning bewaken) voor meer informatie.
+ Gegevensarchieven zoals __Azure Storage-accounts__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__ en __Azure Open Datasets__. Zie [Access data in Azure storage services](how-to-access-data.md) (Toegang tot gegevens in Azure-opslagservices) en [Gegevenssets maken met Azure Open Datasets](how-to-create-register-datasets.md) voor meer informatie.
+ __Virtuele netwerken van Azure__. Zie [Overzicht van isolatie van virtueel netwerk en privacy](how-to-network-security-overview.md).
+ __Azure Pipelines__. Zie [Machine Learning-modellen trainen en implementeren](/azure/devops/pipelines/targets/azure-machine-learning) voor meer informatie.
+ __Logboeken van Git-opslagplaats__. Zie [Git-integratie](concept-train-model-git-integration.md) voor meer informatie.
+ __MLFlow__. Zie [MLFlow om metrische gegevens bij te houden](how-to-use-mlflow.md) en [MLFlow-modellen implementeren als een webservice](how-to-deploy-mlflow-models.md) voor meer informatie 
+ __Kubeflow__. Zie [Build end-to-end workflow pipelines](https://www.kubeflow.org/docs/azure/) (End-to-end werkstroompijplijnen bouwen) voor meer informatie.

### <a name="secure-communications"></a>Beveiligde communicatie

Uw Azure Storage-account, rekendoelen en andere resources kunnen veilig worden gebruikt in een virtueel netwerk om modellen te trainen en deductie uit te voeren. Zie [Overzicht van isolatie van virtueel netwerk en privacy](how-to-network-security-overview.md).

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste experiment met de methode van uw voorkeur:
- + [Aan de slag gaan in uw eigen ontwikkelomgeving](tutorial-1st-experiment-sdk-setup-local.md)
  + [Jupyter-notebooks gebruiken op een rekeninstantie om ML-modellen te trainen en te implementeren](tutorial-1st-experiment-sdk-setup.md)
  + [Geautomatiseerde machine learning gebruiken voor het trainen en implementeren van ML-modellen](tutorial-first-experiment-automated-ml.md) 
  + [Slepen en neerzetten van de ontwerpfunctie gebruiken voor trainen en implementeren](tutorial-designer-automobile-price-train-score.md) 
  + [De machine learning-CLI gebruiken voor het trainen en implementeren van een model](tutorial-train-deploy-model-cli.md)

- Leer meer over [Machine Learning-pijplijnen](concept-ml-pipelines.md) om uw Machine Learning-scenario’s te bouwen, te optimaliseren en te beheren.

- Lees het gedetailleerde artikel [Azure Machine Learning architecture and concepts](concept-azure-machine-learning-architecture.md) (Architectuur en begrippen van Azure Machine Learning).
