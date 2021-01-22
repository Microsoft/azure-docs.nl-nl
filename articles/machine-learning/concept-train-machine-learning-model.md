---
title: '& Train-modellen bouwen'
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen van modellen met Azure Machine Learning. Bekijk de verschillende trainings methoden en kies de juiste methode voor uw project.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: ac9a1b43ba4ffb537afd658c84edb48ed59a3694
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683479"
---
# <a name="train-models-with-azure-machine-learning"></a>Modellen trainen met Azure Machine Learning

Azure Machine Learning biedt verschillende manieren om uw modellen te trainen, van code-eerste oplossingen met behulp van de SDK naar oplossingen met een lage code, zoals geautomatiseerde machine learning en de visuele ontwerper. Gebruik de volgende lijst om te bepalen welke Trainings methode het meest geschikt is voor u:

+ [Azure machine learning SDK voor python](#python-sdk): de PYTHON-SDK biedt verschillende manieren om modellen te trainen, elk met verschillende mogelijkheden.

    | Trainings methode | Beschrijving |
    | ----- | ----- |
    | [Configuratie uitvoeren](#run-configuration) | Een **typische manier om modellen te trainen** is door een trainings script te gebruiken en de configuratie uit te voeren. De uitvoerings configuratie bevat de informatie die nodig is voor het configureren van de trainings omgeving die wordt gebruikt voor het trainen van uw model. U kunt uw trainings script, reken doel en Azure ML-omgeving opgeven in uw uitvoerings configuratie en een trainings taak uitvoeren. |
    | [Geautomatiseerde Machine Learning](#automated-machine-learning) | Met geautomatiseerde machine learning kunt u **modellen trainen zonder uitgebreide data Wetenschappen of programmeer kennis**. Voor mensen met een gegevens wetenschap en-programmeer achtergrond biedt het een manier om tijd en resources te besparen door het automatiseren van algoritme selectie en afstemming-afstemming. U hoeft zich geen zorgen te maken over het definiëren van een configuratie voor het uitvoeren van automatische machine learning. |
    | [Machine learning-pijp lijn](#machine-learning-pipeline) | Pijp lijnen zijn geen andere Trainings methode, maar een **manier om een werk stroom te definiëren met behulp van modulaire, herbruikbare stappen** die training kunnen bevatten als onderdeel van de werk stroom. Machine learning-pijp lijnen ondersteunen het gebruik van geautomatiseerde machine learning en het uitvoeren van configuratie voor het trainen van modellen. Omdat pijp lijnen niet specifiek zijn gericht op training, zijn de redenen voor het gebruik van een pijp lijn meer gevarieerder dan de andere trainings methoden. Over het algemeen kunt u gebruikmaken van een pijp lijn wanneer:<br>* U wilt **processen zonder toezicht plannen** , zoals langlopende trainings taken of gegevens voorbereiding.<br>* Gebruik **meerdere stappen** die worden gecoördineerd over heterogene reken resources en opslag locaties.<br>* Gebruik de pijp lijn als een **herbruikbare sjabloon** voor specifieke scenario's, zoals retraining of batch scores.<br>* **Gegevens bronnen, invoer en uitvoer** voor uw werk stroom bijhouden en controleren.<br>* Uw werk stroom wordt **geïmplementeerd door verschillende teams die onafhankelijk werken met specifieke stappen**. Stappen kunnen vervolgens aan elkaar worden gekoppeld in een pijp lijn om de werk stroom te implementeren. |

+ [Azure machine learning SDK voor r (preview)](#r-sdk-preview): de SDK voor r maakt gebruik van het Reticulate-pakket om een binding te maken met de PYTHON-sdk van Azure machine learning. Hiermee hebt u toegang tot de kern objecten en-methoden die zijn geïmplementeerd in de python-SDK vanuit elke R-omgeving.

+ **Designer**: Azure machine learning Designer biedt een eenvoudig ingangs punt in machine learning voor het bouwen van een proef versie van concepten of voor gebruikers met weinig code ring. Het biedt u de mogelijkheid om modellen te trainen met behulp van een slepen-en-neerzetten op het web. U kunt python-code gebruiken als onderdeel van het ontwerp of modellen trainen zonder code te schrijven.

+ **Cli**: de machine learning cli bevat opdrachten voor algemene taken met Azure machine learning en wordt vaak gebruikt voor het **uitvoeren van scripts en het automatiseren van taken**. Als u bijvoorbeeld een trainings script of pijp lijn hebt gemaakt, kunt u de CLI gebruiken om een training te starten volgens een planning of wanneer de gegevens bestanden die worden gebruikt voor de training, worden bijgewerkt. Voor trainings modellen bevat deze opdrachten voor het verzenden van trainings taken. Het kan taken verzenden met behulp van run-configuraties of-pijp lijnen.

Elk van deze trainings methoden kan gebruikmaken van verschillende soorten reken resources voor training. Deze resources worden gezamenlijk aangeduid als [__Compute-doelen__](concept-azure-machine-learning-architecture.md#compute-targets). Een compute-doel kan een lokale machine of een Cloud resource zijn, zoals een Azure Machine Learning compute, Azure HDInsight of een externe virtuele machine.

## <a name="python-sdk"></a>Python-SDK

Met de Azure Machine Learning SDK voor python kunt u machine learning werk stromen maken en uitvoeren met Azure Machine Learning. U kunt met de service communiceren vanuit een interactieve python-sessie, Jupyter-notebooks, Visual Studio code of een andere IDE.

* [Wat is de Azure Machine Learning SDK voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
* [De SDK installeren/bijwerken](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* [Een ontwikkelingsomgeving voor Azure Machine Learning configureren](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuratie uitvoeren

Een algemene trainings taak met Azure Machine Learning kan worden gedefinieerd met behulp van de [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py). De configuratie voor het uitvoeren van scripts wordt vervolgens gebruikt, samen met uw trainings script (s) voor het trainen van een model op een compute-doel.

U kunt beginnen met het uitvoeren van een configuratie voor uw lokale computer en overschakelen naar een op de cloud gebaseerd reken doel als dat nodig is. Wanneer u het berekenings doel wijzigt, wijzigt u alleen de configuratie van de uitvoering die u gebruikt. Een uitvoering registreert ook informatie over de trainings taak, zoals de invoer, uitvoer en Logboeken.

* [Wat is een uitvoerings configuratie?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Zelfstudie: Uw eerste ML-model trainen](tutorial-1st-experiment-sdk-train.md)
* [Voor beelden: Jupyter Notebook-en python-voor beelden van trainings modellen](https://github.com/Azure/azureml-examples)
* [Procedure: een trainings uitvoering configureren](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Geautomatiseerde machine learning

Definieer de herhalingen, afstemming-instellingen, parametrisatie en andere instellingen. Tijdens de training probeert Azure Machine Learning verschillende algoritmen en para meters parallel. De training stopt zodra de door u opgegeven afsluit criteria zijn gevonden.

> [!TIP]
> Naast de python-SDK kunt u ook gebruikmaken van automatische ML tot en met [Azure machine learning Studio](https://ml.azure.com).

* [Wat is geautomatiseerde machine learning?](concept-automated-ml.md)
* [Zelfstudie: Uw eerste classificatiemodel maken met geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md)
* [Zelfstudie: Geautomatiseerde machine learning gebruiken om taxitarieven te voorspellen](tutorial-auto-train-models.md)
* [Voor beelden: Jupyter Notebook voor beelden voor automatische machine learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Procedure: geautomatiseerde ML experimenten configureren in python](how-to-configure-auto-train.md)
* [Procedure: een prognose model voor een time-reeks autotrainen](how-to-auto-train-forecast.md)
* [Instructies: automatische machine learning experimenten maken, verkennen en implementeren met Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Machine learning-pijp lijn

Machine learning-pijp lijnen kunnen de eerder genoemde trainings methoden gebruiken. Pijp lijnen zijn meer informatie over het maken van een werk stroom, zodat ze meer dan alleen de training van modellen omvatten. In een pijp lijn kunt u een model trainen met geautomatiseerde machine learning of door configuraties uit te voeren.

* [Wat zijn ML-pijp lijnen in Azure Machine Learning?](concept-ml-pipelines.md)
* [machine learning-pijp lijnen maken en uitvoeren met Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Zelf studie: Azure Machine Learning pijplijnen gebruiken voor batch scores](tutorial-pipeline-batch-scoring-classification.md)
* [Voor beelden: Jupyter Notebook voor beelden voor machine learning pijp lijnen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Voor beelden: pijp lijn met automatische machine learning](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Begrijpen wat er gebeurt wanneer u een trainings taak verzendt

De Azure-trainings levenscyclus bestaat uit:

1. Inpakken de bestanden in de projectmap en negeert deze die zijn opgegeven in _. amlignore_ of _. gitignore_
1. Uw berekenings cluster omhoog schalen 
1. De dockerfile maken of downloaden naar het reken knooppunt 
    1. Het systeem berekent een hash van: 
        - De basis installatie kopie 
        - Aangepaste stappen voor docker (Zie [een model implementeren met behulp van een aangepaste docker-basis installatie kopie](./how-to-deploy-custom-docker-image.md))
        - De Conda definition YAML (Zie [& software omgevingen maken in azure machine learning](./how-to-use-environments.md))
    1. Het systeem gebruikt deze hash als de sleutel in een zoek opdracht van de werk ruimte Azure Container Registry (ACR)
    1. Als deze niet wordt gevonden, wordt gezocht naar een overeenkomst in de globale ACR
    1. Als deze niet wordt gevonden, bouwt het systeem een nieuwe installatie kopie (die wordt opgeslagen in de cache en geregistreerd bij de werk ruimte ACR)
1. Het gecomprimeerde project bestand downloaden naar de tijdelijke opslag op het reken knooppunt
1. Het project bestand uitgepakt
1. Het reken knooppunt wordt uitgevoerd `python <entry script> <arguments>`
1. Logboeken, model bestanden en andere bestanden die zijn geschreven naar `./outputs` naar het opslag account dat is gekoppeld aan de werk ruimte opslaan
1. Computer omlaag schalen, inclusief het verwijderen van tijdelijke opslag 

Als u ervoor kiest om op uw lokale machine te trainen ("configureren als lokale uitvoering"), hoeft u docker niet te gebruiken. U kunt docker lokaal gebruiken als u kiest (Zie de sectie [ml-pijp lijn configureren](./how-to-debug-pipelines.md) voor een voor beeld).

## <a name="r-sdk-preview"></a>R SDK (preview-versie)

Met de R-SDK kunt u de R-taal gebruiken met Azure Machine Learning. De SDK gebruikt het Reticulate-pakket om een verbinding te maken met de python-SDK van Azure Machine Learning. Dit geeft u toegang tot de kern objecten en-methoden die zijn geïmplementeerd in de python-SDK vanuit elke R-omgeving.

Raadpleeg voor meer informatie de volgende artikelen:

* [Naslag informatie over Azure Machine Learning SDK voor R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning-ontwerpprogramma

Met de ontwerper kunt u modellen trainen met behulp van een slepen-en-neerzetten-interface in uw webbrowser.

+ [Wat is de ontwerp functie?](concept-designer.md)
+ [Zelf studie: prijs van auto Mobile voors pellen](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>Veel modellen oplossings versneller

De [Many Models Solution Accelerator](https://aka.ms/many-models) (preview) heeft Azure Machine Learning als basis en stelt u in staat om honderden, of zelfs duizenden machine Learning-modellen, te trainen, te gebruiken en te beheren.

Het bouwen van een model __voor elk exemplaar of individu__ in de volgende scenario's kan bijvoorbeeld leiden tot betere resultaten:

* De verkoop voor elke afzonderlijke winkel voors pellen
* Voor speld onderhoud voor honderden olie putjes
* Het aanpassen van een ervaring voor individuele gebruikers.

Zie de [oplossing voor veel modellen van oplossingen](https://aka.ms/many-models) op github voor meer informatie.

## <a name="cli"></a>CLI

De machine learning CLI is een uitbrei ding voor de Azure CLI. Het biedt platformoverschrijdende CLI-opdrachten voor het werken met Azure Machine Learning. Normaal gesp roken gebruikt u de CLI om taken te automatiseren, zoals het trainen van een machine learning model.

* [De CLI-uitbrei ding voor Azure Machine Learning gebruiken](reference-azure-machine-learning-cli.md)
* [MLOps op Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS-code

U kunt de VS code-extensie gebruiken om uw trainings taken uit te voeren en te beheren. Raadpleeg de [hand leiding voor het beheren van bron beheer over](how-to-manage-resources-vscode.md#experiments) meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van een trainings uitvoering](how-to-set-up-training-targets.md).