---
title: Scikit-learn trainen machine learning modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over Azure Machine Learning u in staat stelt om een scikit-learn-trainingsklus uit te schalen met behulp van elastische cloudrekenbronnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 3337607c8e4dd9dca230456cdf268ec3fbfb2f12
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884404"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit-learn-modellen op schaal trainen met Azure Machine Learning

In dit artikel leert u hoe u uw scikit-learn-trainingsscripts kunt uitvoeren met Azure Machine Learning.

De voorbeeldscripts in dit artikel worden gebruikt om irisafbeeldingen te classificeren om een machine learning-model te bouwen op basis van de iris-gegevensset [van scikit-learn.](https://archive.ics.uci.edu/ml/datasets/iris)

Of u nu een machine learning scikit-learn-model vanaf de basis traint of een bestaand model naar de cloud brengt, u kunt Azure Machine Learning gebruiken om opensource-trainingstaken uit te schalen met behulp van elastische cloudrekenbronnen. U kunt modellen van productiekwaliteit bouwen, implementeren, versieverbouwen en bewaken met Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van deze omgevingen:
 - Azure Machine Learning rekenproces : er zijn geen downloads of installatie nodig

    - Voltooi de [Zelfstudie: Omgeving en werkruimte instellen om](tutorial-1st-experiment-sdk-setup.md)  een toegewezen notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de map met voorbeeldentraining op de notebookserver een voltooid en uitgebreid notebook door te navigeren naar deze map: **how-to-use-azureml > ml-frameworks > scikit-learn > train-hyperparameter-tune-deploy-with-sklearn** folder.

 - Uw eigen Jupyter Notebook server

    - [Installeer de Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.13.0).
    - [Maak een configuratiebestand voor de werkruimte.](how-to-configure-environment.md#workspace)

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie stelt u het trainingsexperiment in door de vereiste Python-pakketten te laden, een werkruimte te initialiseren, de trainingsomgeving te definiëren en het trainingsscript voor te bereiden.

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning is](concept-workspace.md) de resource op het hoogste niveau voor de service. Het biedt u een centrale plaats om te werken met alle artefacten die u maakt. In de Python-SDK hebt u toegang tot de werkruimteartefacten door een -object te [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) maken.

Maak een werkruimteobject op basis van `config.json` het bestand dat is gemaakt in de sectie met [vereisten.](#prerequisites)

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Scripts voorbereiden

In deze zelfstudie is het trainingsscript **train_iris.py** hier al [voor u beschikbaar.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py) In de praktijk moet u elk aangepast trainingsscript kunnen uitvoeren met Azure ML zonder dat u uw code moet wijzigen.

Opmerkingen:
- Het opgegeven trainingsscript laat zien hoe u een aantal metrische gegevens in uw Azure ML-run kunt logboeken met behulp van `Run` het -object in het script.
- Het opgegeven trainingsscript maakt gebruik van voorbeeldgegevens van de  `iris = datasets.load_iris()` functie .  Zie Trainen met [gegevenssets](how-to-train-with-datasets.md) om gegevens beschikbaar te maken tijdens de training als u uw eigen gegevens wilt gebruiken en gebruiken.

### <a name="define-your-environment"></a>Uw omgeving definiëren

Als u de Azure [ML-omgeving](concept-environments.md) wilt definiëren waarin de afhankelijkheden van uw trainingsscript zijn ingekapseld, kunt u een aangepaste omgeving definiëren of een door Azure ML gecureerde omgeving gebruiken.

#### <a name="use-a-curated-environment"></a>Een gecureerde omgeving gebruiken
Optioneel biedt Azure ML vooraf samengestelde, gecureerde omgevingen als u uw eigen omgeving niet wilt definiëren. Zie hier voor [meer informatie.](resource-curated-environments.md)
Als u een gecureerde omgeving wilt gebruiken, kunt u in plaats daarvan de volgende opdracht uitvoeren:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Een aangepaste omgeving maken

U kunt ook uw eigen aangepaste omgeving maken. Definieer uw Conda-afhankelijkheden in een YAML-bestand; In dit voorbeeld heeft het bestand de naam `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Maak een Azure ML-omgeving op behulp van deze Conda-omgevingsspecificatie. De omgeving wordt tijdens runtime verpakt in een Docker-container.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Zie Softwareomgevingen maken en gebruiken in Azure Machine Learning voor meer informatie over het maken [en gebruiken van omgevingen.](how-to-use-environments.md)

## <a name="configure-and-submit-your-training-run"></a>De trainingsrun configureren en verzenden

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken
Maak een ScriptRunConfig-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren.
Eventuele argumenten voor uw trainingsscript worden doorgegeven via de opdrachtregel als deze is opgegeven in de `arguments` parameter .

Met de volgende code wordt een ScriptRunConfig-object geconfigureerd voor het verzenden van uw taak voor uitvoering op uw lokale computer.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Als u in plaats daarvan uw taak wilt uitvoeren op een extern cluster, kunt u het gewenste rekendoel opgeven voor de `compute_target` parameter scriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Uw run verzenden
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning voert trainingsscripts uit door de volledige bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [.ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neem u het niet op in de bronmap . Open in plaats daarvan uw gegevens met behulp van een Azure [ML-gegevensset](how-to-train-with-datasets.md).

### <a name="what-happens-during-run-execution"></a>Wat gebeurt er tijdens de uitvoering
Wanneer de uitvoering wordt uitgevoerd, doorloop deze de volgende fasen:

- **Voorbereiden:** er wordt een Docker-afbeelding gemaakt op basis van de omgeving die is gedefinieerd. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere runs. Logboeken worden ook gestreamd naar de uitvoeringsgeschiedenis en kunnen worden bekeken om de voortgang te controleren. Als in plaats daarvan een gecureerde omgeving wordt opgegeven, wordt de back-back-van die gecureerde omgeving in de cache gebruikt.

- **Schalen:** het cluster probeert omhoog te schalen als het Batch AI cluster meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren:** alle scripts in de scriptmap worden geüpload naar het rekendoel, gegevensopslag worden aan of gekopieerd en de `script` wordt uitgevoerd. Uitvoer van stdout en de **map ./logs** worden gestreamd naar de uitvoergeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Naverwerking:** de **map ./outputs** van de run wordt gekopieerd naar de uitvoergeschiedenis.

## <a name="save-and-register-the-model"></a>Het model opslaan en registreren

Zodra u het model hebt getraind, kunt u het opslaan en registreren in uw werkruimte. Met modelregistratie kunt u uw modellen opslaan en versiebeheer in uw werkruimte gebruiken om [het modelbeheer en de implementatie te vereenvoudigen.](concept-model-management-and-deployment.md)

Voeg de volgende code toe aan uw trainingsscript, train_iris.py, om het model op te slaan. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registreer het model bij uw werkruimte met de volgende code. Door de parameters `model_framework` , en op te `model_framework_version` `resource_configuration` geven, wordt de implementatie van het model zonder code beschikbaar. Met modelimplementatie zonder code kunt u uw model rechtstreeks implementeren als een webservice vanuit het geregistreerde model. Het object definieert de [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) rekenresource voor de webservice.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Implementatie

Het model dat u zojuist hebt geregistreerd, kan op exact dezelfde manier worden geïmplementeerd als elk ander geregistreerd model in Azure ML. De implementatie-how-to bevat een sectie over het registreren van modellen, maar u kunt direct naar het maken van een [rekendoel](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie gaan, omdat u al een geregistreerd model hebt.

### <a name="preview-no-code-model-deployment"></a>(Preview) Implementatie van model zonder code

In plaats van de traditionele implementatieroute kunt u ook de implementatiefunctie zonder code (preview) voor scikit-learn gebruiken. Implementatie van model zonder code wordt ondersteund voor alle ingebouwde scikit-learn-modeltypen. Door uw model te registreren zoals hierboven wordt weergegeven met de parameters , en , kunt u gewoon de statische functie gebruiken `model_framework` om uw model te `model_framework_version` `resource_configuration` [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) implementeren.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

OPMERKING: Deze afhankelijkheden zijn opgenomen in de vooraf gebouwde scikit-learn-deference-container.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

De volledige [informatie over implementatie](how-to-deploy-and-where.md) in Azure Machine Learning uitgebreider.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een scikit-learn-model getraind en geregistreerd en meer geleerd over implementatieopties. Zie deze andere artikelen voor meer informatie over Azure Machine Learning.

* [Metrische gegevens van de run bijhouden tijdens de training](how-to-log-view-metrics.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
