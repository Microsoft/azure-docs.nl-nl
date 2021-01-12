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
ms.openlocfilehash: a5764a9f230540d58edf71e8c00781e86589aa9a
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070164"
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

## <a name="troubleshooting"></a>Problemen oplossen

 * **ModuleErrors (geen module met de naam)**: als u in ModuleErrors gebruikt tijdens het verzenden van experimenten in azure ml, verwacht het trainings script een pakket dat moet worden geïnstalleerd, maar het wordt niet toegevoegd. Wanneer u de naam van het pakket opgeeft, installeert Azure ML het pakket in de omgeving die wordt gebruikt voor de uitvoering van uw training.

    Als u schattingen gebruikt om experimenten in te dienen, kunt u een pakket naam opgeven via `pip_packages` of `conda_packages` para meter in de Estimator op basis van de bron die u wilt installeren van het pakket. U kunt ook een yml-bestand met al uw afhankelijkheden opgeven `conda_dependencies_file` of al uw PIP-vereisten in een txt-bestand met behulp van `pip_requirements_file` para meter. Als u uw eigen Azure ML-omgevings object hebt dat u de standaard installatie kopie wilt overschrijven die wordt gebruikt door de Estimator, kunt u die omgeving opgeven via de `environment` para meter van de Estimator-constructor.
    
    Azure ML bewaart docker-installatie kopieën en de inhoud ervan kunnen worden weer gegeven in [AzureML-containers](https://github.com/Azure/AzureML-Containers).
    Framework-specifieke afhankelijkheden worden vermeld in de betreffende Framework-documentatie:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    
    > [!Note]
    > Als u denkt dat een bepaald pakket algemeen genoeg is om te worden toegevoegd aan de bewaarde afbeeldingen en omgevingen van Azure ML, kunt u een GitHub-probleem veroorzaken in [AzureML-containers](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (naam niet gedefinieerd), AttributeError (object heeft geen kenmerk)**: Deze uitzonde ring moet afkomstig zijn uit uw trainings scripts. U kunt de logboek bestanden van Azure Portal bekijken voor meer informatie over de specifieke naam niet gedefinieerd of kenmerk fout. U kunt de SDK gebruiken `run.get_details()` om het fout bericht te bekijken. Hiermee worden ook alle logboek bestanden weer geven die zijn gegenereerd voor de uitvoering. Bekijk uw trainings script en los het probleem op voordat u de uitvoering opnieuw verzendt. 


* **Verwijdering van de uitvoering of het experiment**: experimenten kunnen worden gearchiveerd met behulp van de methode [experiment. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) of vanuit de weer gave van het tabblad experiment in azure machine learning Studio-client via de knop ' Archief experiment '. Met deze actie wordt het experiment verborgen in lijst query's en weer gaven, maar wordt het niet verwijderd.

    Het definitief verwijderen van afzonderlijke experimenten of uitvoeringen wordt momenteel niet ondersteund. Zie [uw machine learning service-werkruimte gegevens exporteren of verwijderen](how-to-export-delete-data.md)voor meer informatie over het verwijderen van werk ruimte-assets.

* Het **metrieke document is te groot**: Azure machine learning heeft interne limieten voor de grootte van metrische objecten die tegelijk kunnen worden geregistreerd vanuit een training-uitvoering. Als er een foutbericht wordt weergegeven dat het metrische document te groot is bij het vastleggen van een metrische waarde voor een lijst, kunt u de lijst in kleinere segmenten splitsen, bijvoorbeeld:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Azure ML voegt de blokken met dezelfde metrische-gegevensnaam intern samen tot een aaneengesloten lijst.

* **Uitvoeren mislukt met `jwt.exceptions.DecodeError`**: exact fout bericht: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    U kunt een upgrade uitvoeren naar de nieuwste versie van azureml-core: `pip install -U azureml-core` .
    
    Als u dit probleem voor lokale uitvoeringen ondervindt, controleert u de versie van PyJWT die in uw omgeving is geïnstalleerd. De ondersteunde versies van PyJWT zijn < 2.0.0. Verwijder PyJWT uit de omgeving als de versie >= 2.0.0 is. U kunt de versie van PyJWT controleren, de juiste versie als volgt verwijderen en installeren:
    1. Start een opdracht shell, activeer de Conda-omgeving waarin de azureml-kern is geïnstalleerd.
    2. Voer `pip freeze` in en zoek naar `PyJWT` , indien gevonden, de weer gegeven versie < 2.0.0
    3. Als de vermelde versie geen ondersteunde versie is, `pip uninstall PyJWT` typt u in de opdracht shell en voert u y in voor bevestiging.
    4. Installeren met behulp van `pip install 'PyJWT<2.0.0'`
    
    Als u een door de gebruiker gemaakte omgeving met uw run verzendt, kunt u overwegen de nieuwste versie van de azureml-core in die omgeving te gebruiken. Versies >= 1.18.0 van azureml-core al pincode PyJWT < 2.0.0. Als u een versie van de azureml-core < 1.18.0 moet gebruiken in de omgeving die u verzendt, moet u ervoor zorgen dat u PyJWT < 2.0.0 in uw PIP-afhankelijkheden opgeeft.

* Het starten van het **reken doel duurt erg lang**: de docker-installatie kopieën voor reken doelen worden geladen van Azure container Registry (ACR). Azure Machine Learning maakt standaard een ACR die gebruikmaakt van de *Basic* -servicelaag. Het wijzigen van de ACR voor uw werk ruimte in de standaard-of Premium-laag kan de tijd verminderen die nodig is om installatie kopieën te bouwen en te laden. Zie [Azure container Registry service lagen](../container-registry/container-registry-skus.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een model trainen](tutorial-train-models-with-aml.md) maakt gebruik van een beheerd Compute-doel om een model te trainen.
* Zie modellen trainen met specifieke ML-frameworks, zoals [Scikit-Learn](how-to-train-scikit-learn.md), [tensor flow](how-to-train-tensorflow.md)en [PyTorch](how-to-train-pytorch.md).
* Meer informatie over hoe u [Hyper parameters efficiënt kunt afstemmen](how-to-tune-hyperparameters.md) om betere modellen te bouwen.
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* Bekijk de [ScriptRunConfig class](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) SDK-referentie.
* [Azure Machine Learning gebruiken met virtuele netwerken van Azure](./how-to-network-security-overview.md)
