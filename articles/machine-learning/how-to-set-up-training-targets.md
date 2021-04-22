---
title: Een trainingsrun configureren
titleSuffix: Azure Machine Learning
description: Train uw machine learning in verschillende trainingsomgevingen (rekendoelen). U kunt eenvoudig schakelen tussen trainingsomgevingen.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: e044920953d0b635e7fe92f07b2cd69c0d8f5ee7
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888580"
---
# <a name="configure-and-submit-training-runs"></a>Trainingsuitvoering configureren en verzenden

In dit artikel leert u hoe u de uitvoeringen kunt configureren en Azure Machine Learning om uw modellen te trainen. Codefragmenten geven uitleg over de belangrijkste onderdelen van de configuratie en het indienen van een trainingsscript.  Gebruik vervolgens een van de [voorbeeldnote notebooks om](#notebooks) de volledige end-to-end werkende voorbeelden te vinden.

Tijdens de training is het gebruikelijk om op uw lokale computer te beginnen en later uit te schalen naar een cloudcluster. Met Azure Machine Learning kunt u uw script uitvoeren op verschillende rekendoelen zonder dat u uw trainingsscript moet wijzigen.

Het enige wat u hoeft te doen, is de omgeving definiëren voor elk rekendoel binnen een **scriptuit voerconfiguratie**.  Als u vervolgens uw trainingsexperiment wilt uitvoeren op een ander rekendoel, geeft u de configuratie van de uitvoering voor die berekening op.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maak dan een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree) vandaag nog
* De [Azure Machine Learning-SDK voor Python](/python/api/overview/azure/ml/install) (>= 1.13.0)
* Een [Azure Machine Learning werkruimte](how-to-manage-workspace.md), `ws`
* Een rekendoel, `my_compute_target` .  [Een rekendoel maken](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Wat is een configuratie voor het uitvoeren van scripts?
Een [ScriptRunConfig wordt](/python/api/azureml-core/azureml.core.scriptrunconfig) gebruikt om de informatie te configureren die nodig is voor het verzenden van een trainingsuit voeren als onderdeel van een experiment.

U dient uw trainingsexperiment in met een ScriptRunConfig-object.  Dit object bevat de:

* **source_directory:** de bronmap met uw trainingsscript
* **script:** het trainingsscript dat moet worden uitgevoerd
* **compute_target:** het rekendoel om op uit te voeren
* **environment:** de omgeving die moet worden gebruikt bij het uitvoeren van het script
* en enkele aanvullende configureerbare opties (zie de [referentiedocumentatie](/python/api/azureml-core/azureml.core.scriptrunconfig) voor meer informatie)

## <a name="train-your-model"></a><a id="submit"></a>Uw model trainen

Het codepatroon voor het verzenden van een trainingsuit voer is hetzelfde voor alle typen rekendoelen:

1. Een experiment maken om uit te voeren
1. Een omgeving maken waarin het script wordt uitgevoerd
1. Een ScriptRunConfig maken, waarmee het rekendoel en de omgeving worden opgegeven
1. De run verzenden
1. Wacht tot de run is voltooid

U kunt ook het volgende doen:

* Verzend een HyperDrive-run voor [het afstemmen van hyperparameters.](how-to-tune-hyperparameters.md)
* Verzend een experiment via de [VS Code-extensie](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Een experiment maken

Maak een experiment in uw werkruimte.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Een rekendoel selecteren

Selecteer het rekendoel waarop uw trainingsscript wordt uitgevoerd. Als er geen rekendoel is opgegeven in scriptRunConfig, of als , wordt uw `compute_target='local'` script lokaal door Azure ML uitgevoerd. 

In de voorbeeldcode in dit artikel wordt ervan uitgenomen dat u al een rekendoel hebt gemaakt in de `my_compute_target` sectie Vereisten.

>[!Note]
>Azure Databricks wordt niet ondersteund als een rekendoel voor modeltraining. U kunt deze Azure Databricks voor gegevensvoorbereidings- en implementatietaken. 

## <a name="create-an-environment"></a>Een omgeving maken
Azure Machine Learning [omgevingen](concept-environments.md) zijn een inkapseling van de omgeving waarin uw machine learning training gebeurt. Ze geven de Python-pakketten, Docker-afbeelding, omgevingsvariabelen en software-instellingen rond uw trainings- en scorescripts op. Ze geven ook runtimes op (Python, Spark of Docker).

U kunt uw eigen omgeving definiëren of een door Azure ML gecureerde omgeving gebruiken. [Gecureerde omgevingen](./how-to-use-environments.md#use-a-curated-environment) zijn vooraf gedefinieerde omgevingen die standaard beschikbaar zijn in uw werkruimte. Deze omgevingen worden geback-byd door Docker-afbeeldingen in de cache, waardoor de kosten voor de voorbereiding van de run worden beperkt. Zie [Azure Machine Learning gecureerde omgevingen](./resource-curated-environments.md) voor de volledige lijst met beschikbare gecureerde omgevingen.

Voor een extern rekendoel kunt u beginnen met een van deze populaire gecureerde omgevingen:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Zie Create & use software environments in Azure Machine Learning [(Softwareomgevingen](how-to-use-environments.md)gebruiken in Azure Machine Learning) voor meer informatie over omgevingen.
  
### <a name="local-compute-target"></a><a name="local"></a>Lokaal rekendoel

Als uw rekendoel uw lokale **computer** is, bent u verantwoordelijk voor het zorgen dat alle benodigde pakketten beschikbaar zijn in de Python-omgeving waarin het script wordt uitgevoerd.  Gebruik `python.user_managed_dependencies` om uw huidige Python-omgeving te gebruiken (of de Python op het pad dat u opgeeft).

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>De configuratie voor het uitvoeren van het script maken

Nu u een rekendoel ( ) en omgeving ( ) hebt, maakt u een scriptrunconfiguratie die uw `my_compute_target` `myenv` trainingsscript () in uw map wordt `train.py` `project_folder` uitgevoerd:

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

Als u geen omgeving opgeeft, wordt er een standaardomgeving voor u gemaakt.

Als u opdrachtregelargumenten hebt die u wilt doorgeven aan uw trainingsscript, kunt u deze opgeven via de parameter van de **`arguments`** ScriptRunConfig-constructor, bijvoorbeeld `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Als u de standaard maximale tijd voor de run wilt overschrijven, kunt u dit doen via de **`max_run_duration_seconds`** parameter . Het systeem probeert de run automatisch te annuleren als dit langer duurt dan deze waarde.

### <a name="specify-a-distributed-job-configuration"></a>Een configuratie voor gedistribueerde taak opgeven
Als u een gedistribueerde trainings job wilt uitvoeren, geeft u de gedistribueerde taakspecifieke configuratie op voor de **`distributed_job_config`** parameter . Ondersteunde configuratietypen zijn [MpiConfiguration,](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)en [PyTorchConfiguration.](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) 

Zie voor meer informatie en voorbeelden over het uitvoeren van gedistribueerde Horovod-, TensorFlow- en PyTorch-taken:

* [TensorFlow-modellen trainen](./how-to-train-tensorflow.md#distributed-training)
* [PyTorch-modellen trainen](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Het experiment verzenden

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Wanneer u de trainingsrun indient, wordt er een momentopname van de map met uw trainingsscripts gemaakt en verzonden naar het rekendoel. Het wordt ook opgeslagen als onderdeel van het experiment in uw werkruimte. Als u bestanden wijzigt en de run opnieuw indient, worden alleen de gewijzigde bestanden geüpload.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Zie Momentopnamen voor meer informatie [over momentopnamen.](concept-azure-machine-learning-architecture.md#snapshots)

> [!IMPORTANT]
> **Speciale mappen** Twee mappen, uitvoer *en* *logboeken,* krijgen een speciale behandeling door Azure Machine Learning. Wanneer u tijdens de training bestanden schrijft  naar mappen met de naam *outputs* en logboeken die relatief zijn ten opzichte van respectievelijk de hoofdmap ( en ), worden de bestanden automatisch geüpload naar uw uitvoergeschiedenis, zodat u er toegang toe hebt zodra de `./outputs` run is `./logs` voltooid.
>
> Als u artefacten wilt maken tijdens de training (zoals modelbestanden, controlepunten, gegevensbestanden of uitgezete afbeeldingen), schrijft u deze naar de `./outputs` map .
>
> Op dezelfde manier kunt u logboeken van uw trainingsrun naar de map `./logs` schrijven. Als u Azure Machine Learning [TensorBoard-integratie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) wilt gebruiken, moet u uw TensorBoard-logboeken naar deze map schrijven. Terwijl de uitvoering wordt uitgevoerd, kunt u TensorBoard starten en deze logboeken streamen.  Later kunt u ook de logboeken herstellen vanuit een van de vorige runs.
>
> Als u bijvoorbeeld een bestand wilt downloaden dat naar de *map outputs* is geschreven naar uw lokale computer nadat u de externe training hebt uitgevoerd: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git-tracering en -integratie

Wanneer u een trainingsrun start waarbij de bronmap een lokale Git-opslagplaats is, wordt informatie over de opslagplaats opgeslagen in de geschiedenis van de run. Zie Git-integratie voor [meer informatie Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Notebook-voorbeelden

Zie deze notebooks voor voorbeelden van het configureren van runs voor verschillende trainingsscenario's:
* [Training voor verschillende rekendoelen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Trainen met ML-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Problemen oplossen

* **Uitvoeren mislukt `jwt.exceptions.DecodeError` met**: Exacte foutbericht: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    U kunt upgraden naar de nieuwste versie van azureml-core: `pip install -U azureml-core` .
    
    Als u met dit probleem te maken hebt bij lokale uitvoeringen, controleert u de versie van PyJWT die is geïnstalleerd in uw omgeving waar u uitvoeringen start. De ondersteunde versies van PyJWT zijn < 2.0.0. Verwijder PyJWT uit de omgeving als de versie >= 2.0.0. U kunt als volgt de versie van PyJWT controleren, de juiste versie verwijderen en installeren:
    1. Start een opdrachtshell en activeer de Conda-omgeving waarin azureml-core is geïnstalleerd.
    2. Voer in en zoek naar . Indien gevonden, moet de vermelde versie `pip freeze` `PyJWT` < 2.0.0 zijn
    3. Als de vermelde versie geen ondersteunde versie is, voert u in de `pip uninstall PyJWT` opdrachtshell y in ter bevestiging.
    4. Installeren met behulp van `pip install 'PyJWT<2.0.0'`
    
    Als u een door de gebruiker gemaakte omgeving indient bij uw uitvoering, kunt u overwegen de nieuwste versie van azureml-core in die omgeving te gebruiken. Versies >= 1.18.0 van azureml-core pin al PyJWT < 2.0.0. Als u een versie van azureml-core < 1.18.0 moet gebruiken in de omgeving die u indient, moet u PyJWT < 2.0.0 opgeven in uw PIP-afhankelijkheden.


 * **ModuleErrors (geen module** met de naam) : als u ModuleErrors tegen komt tijdens het verzenden van experimenten in Azure ML, verwacht het trainingsscript dat er een pakket wordt geïnstalleerd, maar dit wordt niet toegevoegd. Nadat u de pakketnaam hebt verstrekt, installeert Azure ML het pakket in de omgeving die wordt gebruikt voor de trainingsrun.

    Als u estimators gebruikt om experimenten in te dienen, kunt u een pakketnaam of parameter opgeven in de estimator op basis van de bron waaruit u `pip_packages` `conda_packages` het pakket wilt installeren. U kunt ook een yml-bestand met al uw afhankelijkheden opgeven met behulp van of al uw PIP-vereisten in een `conda_dependencies_file` txt-bestand vermelden met behulp van `pip_requirements_file` de parameter . Als u uw eigen Azure ML Environment-object hebt dat u wilt overschrijven van de standaardafbeelding die wordt gebruikt door de estimator, kunt u die omgeving opgeven via de parameter van de `environment` estimator-constructor.
    
    In Azure ML onderhouden Docker-afbeeldingen en de inhoud ervan kunt u zien in [AzureML-containers.](https://github.com/Azure/AzureML-Containers)
    Frameworkspecifieke afhankelijkheden worden vermeld in de respectieve frameworkdocumentatie:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > Als u denkt dat een bepaald pakket algemeen genoeg is om te worden toegevoegd aan in Azure ML onderhouden afbeeldingen en omgevingen, moet u een GitHub-probleem in [AzureML Containers aanleveren.](https://github.com/Azure/AzureML-Containers) 
 
* **NameError (naam niet gedefinieerd), AttributeError (object heeft** geen kenmerk) : deze uitzondering moet afkomstig zijn van uw trainingsscripts. U kunt de logboekbestanden van Azure Portal voor meer informatie over de specifieke naam die niet is gedefinieerd of kenmerkfout. Vanuit de SDK kunt u gebruiken om `run.get_details()` het foutbericht te bekijken. Hiermee worden ook alle logboekbestanden weergegeven die zijn gegenereerd voor uw run. Bekijk uw trainingsscript en los de fout op voordat u de run opnieuw inzendt. 


* **Uitvoeren of experiment verwijderen:** experimenten kunnen worden gearchiveerd met behulp van de methode [Experiment.archive](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) of vanuit de tabbladweergave Experiment in Azure Machine Learning-studio client via de knop Archive experiment. Met deze actie wordt het experiment verborgen in lijstquery's en weergaven, maar wordt het niet verwijderd.

    Het definitief verwijderen van afzonderlijke experimenten of uitvoeringen wordt momenteel niet ondersteund. Zie Uw werkruimtegegevens exporteren of verwijderen voor meer informatie over het verwijderen [Machine Learning service werkruimtegegevens.](how-to-export-delete-data.md)

* **Document met metrische gegevens is** te groot: Azure Machine Learning interne limieten voor de grootte van metrische objecten die in één keer kunnen worden geregistreerd tijdens een trainingsrun. Als er een foutbericht wordt weergegeven dat het metrische document te groot is bij het vastleggen van een metrische waarde voor een lijst, kunt u de lijst in kleinere segmenten splitsen, bijvoorbeeld:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Azure ML voegt de blokken met dezelfde metrische-gegevensnaam intern samen tot een aaneengesloten lijst.

* **Het duurt lang om het rekendoel** te starten: de Docker-afbeeldingen voor rekendoelen worden vanuit Azure Container Registry (ACR) geladen. Standaard maakt Azure Machine Learning een ACR die gebruikmaakt van de basic-servicelaag.  Het wijzigen van de ACR voor uw werkruimte in de Standard- of Premium-laag kan de tijd verminderen die nodig is om afbeeldingen te bouwen en te laden. Zie servicelagen voor [Azure Container Registry meer informatie.](../container-registry/container-registry-skus.md)

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Een model trainen maakt](tutorial-train-models-with-aml.md) gebruik van een beheerd rekendoel om een model te trainen.
* Ontdek hoe u modellen traint met specifieke ML-frameworks, zoals [Scikit-learn,](how-to-train-scikit-learn.md) [TensorFlow](how-to-train-tensorflow.md)en [PyTorch.](how-to-train-pytorch.md)
* Meer informatie over het [efficiënt afstemmen van hyperparameters om](how-to-tune-hyperparameters.md) betere modellen te bouwen.
* Zodra u een getraind model hebt, leert [u hoe en waar u modellen implementeert.](how-to-deploy-and-where.md)
* Bekijk de [naslag voor de ScriptRunConfig-klasse-SDK.](/python/api/azureml-core/azureml.core.scriptrunconfig)
* [Een Azure Machine Learning azure virtual networks gebruiken](./how-to-network-security-overview.md)
