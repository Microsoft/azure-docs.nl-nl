---
title: Een trainings uitvoering configureren
titleSuffix: Azure Machine Learning
description: Train uw machine learning model op verschillende trainings omgevingen (reken doelen). U kunt eenvoudig overschakelen tussen trainings omgevingen.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 2197d5be91af4c93e9691e1dc2b953198669deaf
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027404"
---
# <a name="configure-and-submit-training-runs"></a>Trainingsuitvoering configureren en verzenden

In dit artikel leert u hoe u Azure Machine Learning uitvoeringen kunt configureren en verzenden om uw modellen te trainen. De code fragmenten van een programma bevatten uitleg over de belangrijkste onderdelen van de configuratie en het indienen van een trainings script.  Gebruik vervolgens een van de [voorbeeld notitieblokken](#notebooks) om de volledige end-to-end-werk voorbeelden te vinden.

Bij het trainen is het gebruikelijk om te beginnen op de lokale computer en vervolgens te schalen naar een Cloud cluster. Met Azure Machine Learning kunt u uw script uitvoeren op verschillende reken doelen zonder dat u uw trainings script hoeft te wijzigen.

Het enige wat u hoeft te doen, is het definiëren van de omgeving voor elk reken doel in een **script configuratie uitvoeren**.  Wanneer u uw trainings experiment wilt uitvoeren op een ander Compute-doel, geeft u de uitvoerings configuratie op voor die reken kracht.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maak dan een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree)
* De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0)
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md), `ws`
* Een reken doel, `my_compute_target` .  [Een rekendoel maken](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Wat is een script voor het uitvoeren van een configuratie?
Een [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) wordt gebruikt voor het configureren van de informatie die nodig is voor het indienen van een training die als onderdeel van een experiment moet worden uitgevoerd.

U verzendt uw trainings experiment met een ScriptRunConfig-object.  Dit object bevat:

* **source_directory**: de bron directory die uw trainings script bevat
* **script**: het trainings script dat moet worden uitgevoerd
* **compute_target**: het Compute-doel om uit te voeren
* **omgeving**: de omgeving die moet worden gebruikt bij het uitvoeren van het script
* en een aantal extra Configureer bare opties (Zie de [referentie documentatie](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) voor meer informatie)

## <a name="train-your-model"></a><a id="submit"></a>Uw model trainen

Het code patroon voor het verzenden van een trainings uitvoering is hetzelfde voor alle typen reken doelen:

1. Een experiment maken om uit te voeren
1. Een omgeving maken waarin het script wordt uitgevoerd
1. Een ScriptRunConfig maken, waarmee het doel en de omgeving van de berekening worden opgegeven
1. De uitvoering verzenden
1. Wacht totdat de uitvoering is voltooid

U kunt ook het volgende doen:

* Verzend een HyperDrive-uitvoering voor [afstemming-afstemming](how-to-tune-hyperparameters.md).
* Een experiment verzenden via de [VS code-extensie](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Een experiment maken

Maak een experiment in uw werk ruimte.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Een reken doel selecteren

Selecteer het berekenings doel waar uw trainings script wordt uitgevoerd. Als er geen reken doel is opgegeven in de ScriptRunConfig of als `compute_target='local'` , wordt het script lokaal door Azure ml uitgevoerd. 

In de voorbeeld code in dit artikel wordt ervan uitgegaan dat u al een reken doel hebt gemaakt `my_compute_target` in de sectie vereisten.

## <a name="create-an-environment"></a>Een omgeving maken
Azure Machine Learning [omgevingen](concept-environments.md) zijn een inkapseling van de omgeving waarin uw machine learning-training zich voordoet. Hiermee worden de Python-pakketten, docker-installatie kopie, omgevings variabelen en software-instellingen voor uw trainings-en Score scripts opgegeven. Ze geven ook runtime (python, Spark, of docker) op.

U kunt uw eigen omgeving definiëren of een Azure ML-omgeving gebruiken. Gehoste [omgevingen](./how-to-use-environments.md#use-a-curated-environment) zijn vooraf gedefinieerde omgevingen die standaard beschikbaar zijn in uw werk ruimte. Deze omgevingen worden ondersteund door docker-installatie kopieën in de cache, waarmee de kosten voor het uitvoeren van de voor bereiding worden verminderd. Zie [Azure machine learning Gecuratorde omgevingen](./resource-curated-environments.md) voor een volledige lijst met beschik bare, gecuratorde omgevingen.

Voor een extern Compute-doel kunt u een van deze populaire gestarte omgevingen gebruiken om te beginnen met:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Zie [& software omgevingen maken in azure machine learning](how-to-use-environments.md)voor meer informatie over omgevingen.
  
### <a name="local-compute-target"></a><a name="local"></a>Lokaal Compute-doel

Als uw reken doel uw **lokale computer** is, bent u verantwoordelijk voor het controleren of alle benodigde pakketten beschikbaar zijn in de python-omgeving waarin het script wordt uitgevoerd.  Gebruik `python.user_managed_dependencies` om uw huidige python-omgeving (of de python op het door u opgegeven pad) te gebruiken.

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>De configuratie voor het uitvoeren van scripts maken

Nu u een compute-doel ( `my_compute_target` ) en-omgeving ( `myenv` ) hebt, maakt u een script voor het uitvoeren van een configuratie waarbij uw trainings script () wordt uitgevoerd `train.py` in uw `project_folder` map:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Als u geen omgeving opgeeft, wordt er een standaard omgeving voor u gemaakt.

Als u opdracht regel argumenten hebt die u wilt door geven aan uw trainings script, kunt u deze opgeven via de **`arguments`** para meter van de ScriptRunConfig-constructor, bijvoorbeeld `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Als u de standaard toegestane maximum tijd voor de uitvoering wilt onderdrukken, kunt u dit doen via de **`max_run_duration_seconds`** para meter. Het systeem probeert de uitvoering automatisch te annuleren als deze langer duurt dan deze waarde.

### <a name="specify-a-distributed-job-configuration"></a>Configuratie van een gedistribueerde taak opgeven
Als u een gedistribueerde trainings taak wilt uitvoeren, geeft u de gedistribueerde taak-specifieke configuratie op voor de **`distributed_job_config`** para meter. Ondersteunde configuratie typen zijn [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)en [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py). 

Zie voor meer informatie en voor beelden over het uitvoeren van gedistribueerde Horovod-, tensor flow-en PyTorch-taken:

* [TensorFlow-modellen trainen](./how-to-train-tensorflow.md#distributed-training)
* [PyTorch-modellen trainen](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Het experiment verzenden

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Wanneer u de trainings uitvoering verzendt, wordt een moment opname gemaakt van de map die uw trainings scripts bevat en verzonden naar het doel van de berekening. Het wordt ook opgeslagen als onderdeel van het experiment in uw werk ruimte. Als u bestanden wijzigt en de uitvoering opnieuw verzendt, worden alleen de gewijzigde bestanden geüpload.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Zie [moment opnamen](concept-azure-machine-learning-architecture.md#snapshots)voor meer informatie over moment opnamen.

> [!IMPORTANT]
> **Speciale mappen** Twee mappen, *uitvoer* en *Logboeken*, een speciale behandeling ontvangen door Azure machine learning. Wanneer u tijdens de training bestanden schrijft naar mappen met de naam *outputs* en *Logboeken* die relatief zijn ten opzichte van de hoofdmap ( `./outputs` en `./logs` respectievelijk), worden de bestanden automatisch geüpload naar de uitvoerings geschiedenis, zodat u er toegang tot hebt zodra de uitvoering is voltooid.
>
> Als u artefacten wilt maken tijdens de training (zoals model bestanden, controle punten, gegevens bestanden of geplote afbeeldingen), schrijft u deze naar de `./outputs` map.
>
> Op dezelfde manier kunt u de logboeken van uw trainings uitvoering naar de `./logs` map schrijven. Als u de TensorBoard- [integratie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) van Azure machine learning wilt gebruiken, moet u ervoor zorgen dat u uw TensorBoard-logboeken naar deze map schrijft. Tijdens het uitvoeren van de uitvoering kunt u TensorBoard starten en deze logboeken streamen.  Later kunt u de logboeken ook herstellen vanuit een van de vorige uitvoeringen.
>
> Als u bijvoorbeeld een bestand wilt downloaden naar de map *uitvoer* naar de lokale computer nadat de externe training is uitgevoerd: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git-tracking en-integratie

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. Zie [Git-integratie voor Azure machine learning](concept-train-model-git-integration.md)voor meer informatie.

## <a name="notebook-examples"></a><a name="notebooks"></a>Voor beelden van notebooks

Bekijk deze notebooks voor voor beelden van het configureren van uitvoeringen voor verschillende trainings scenario's:
* [Training over diverse reken doelen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Training met ML Frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [zelf studies/img-Classification-part1-training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een model trainen](tutorial-train-models-with-aml.md) maakt gebruik van een beheerd Compute-doel om een model te trainen.
* Zie modellen trainen met specifieke ML-frameworks, zoals [Scikit-Learn](how-to-train-scikit-learn.md), [tensor flow](how-to-train-tensorflow.md)en [PyTorch](how-to-train-pytorch.md).
* Meer informatie over hoe u [Hyper parameters efficiënt kunt afstemmen](how-to-tune-hyperparameters.md) om betere modellen te bouwen.
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* Bekijk de [ScriptRunConfig class](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) SDK-referentie.
* [Azure Machine Learning gebruiken met virtuele netwerken van Azure](./how-to-network-security-overview.md)
