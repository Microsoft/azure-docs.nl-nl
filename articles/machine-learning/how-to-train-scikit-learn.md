---
title: Scikit trainen-meer informatie over machine learning modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u met behulp van Azure Machine Learning een scikit-leer taak kunt schalen met Elastic Cloud Compute-resources.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 807174fdbede2e4631b3ca1df7220904038da4c8
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518293"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit trainen-modellen op schaal leren met Azure Machine Learning

In dit artikel leert u hoe u uw scikit-trainings scripts kunt uitvoeren met Azure Machine Learning.

De voorbeeld scripts in dit artikel worden gebruikt voor het classificeren van Iris bloem installatie kopieën om een machine learning model te bouwen op basis van de [Iris-gegevensset](https://archive.ics.uci.edu/ml/datasets/iris)van scikit-leer.

Of u nu op de hoogte bent van een machine learning scikit-model of u een bestaand model in de Cloud hebt, kunt u Azure Machine Learning gebruiken om open-source trainings taken uit te schalen met behulp van elastische Cloud Compute-resources. U kunt modellen voor productie kwaliteit bouwen, implementeren, versie en bewaken met Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van de volgende omgevingen:
 - Azure Machine Learning Compute-instantie-geen down loads of installatie vereist

    - Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md)  om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.
    - Zoek in de map met voor beelden-trainingen op de notebook server een volledig en uitgebreid notitie blok door naar deze map te navigeren: **How-to-use-azureml > ml-frameworks > scikit-leer > Train-afstemming-Tune-Deploy-with-sklearn-** map.

 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure machine learning SDK](/python/api/overview/azure/ml/install) (>= 1.13.0).
    - [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainings experiment opgesteld door het laden van de vereiste Python-pakketten, het initialiseren van een werk ruimte, het definiëren van de trainings omgeving en het voorbereiden van het trainings script.

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) object te maken.

Maak een werkruimte object op basis van het `config.json` bestand dat in de [sectie vereisten](#prerequisites)is gemaakt.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Scripts voorbereiden

In deze zelf studie is het trainings script **train_iris. py** al [voor u beschikbaar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). In de praktijk moet u een aangepast trainings script kunnen uitvoeren zoals dat is en dit kan worden uitgevoerd met Azure ML zonder dat u de code hoeft te wijzigen.

Opmerkingen:
- In het meegeleverde trainings script ziet u hoe u met het `Run` object binnen het script enkele metrische gegevens registreert bij uw Azure ml-uitvoering.
- In het meegeleverde trainings script worden voorbeeld gegevens van de  `iris = datasets.load_iris()` functie gebruikt.  Zie [How to Train with data sets](how-to-train-with-datasets.md) om gegevens beschikbaar te maken tijdens de training om uw eigen gegevens te gebruiken en te openen.

### <a name="define-your-environment"></a>Uw omgeving definiëren

Als u de Azure ML- [omgeving](concept-environments.md) wilt definiëren waarin de afhankelijkheden van uw trainings script worden ingekapseld, kunt u een aangepaste omgeving of gebruik en Azure ml-omgeving definiëren.

#### <a name="use-a-curated-environment"></a>Een gecuratore omgeving gebruiken
Azure ML biedt eventueel vooraf ontwikkelde, gegroepeerde omgevingen als u uw eigen omgeving niet wilt definiëren. Zie [hier](resource-curated-environments.md)voor meer informatie.
Als u een gewerkte omgeving wilt gebruiken, kunt u in plaats daarvan de volgende opdracht uitvoeren:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Een aangepaste omgeving maken

U kunt ook uw eigen aangepaste omgeving maken. Definieer uw Conda-afhankelijkheden in een YAML-bestand. in dit voor beeld heeft het bestand de naam `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Een Azure ML-omgeving maken op basis van deze Conda Environment-specificatie. De omgeving wordt tijdens runtime ingepakt in een docker-container.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Zie [software omgevingen maken en gebruiken in azure machine learning](how-to-use-environments.md)voor meer informatie over het maken van en het gebruik van omgevingen.

## <a name="configure-and-submit-your-training-run"></a>Uw trainings uitvoering configureren en verzenden

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken
Maak een ScriptRunConfig-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren.
Eventuele argumenten voor uw trainings script worden door gegeven via de opdracht regel indien opgegeven in de `arguments` para meter.

Met de volgende code wordt een ScriptRunConfig-object geconfigureerd voor het verzenden van uw taak voor uitvoering op uw lokale computer.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Als u in plaats daarvan uw taak wilt uitvoeren op een extern cluster, kunt u het gewenste Compute-doel opgeven voor de `compute_target` para meter ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Uw uitvoering verzenden
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning trainings scripts worden uitgevoerd door de hele bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [. ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neemt u het niet op in de bron directory. In plaats daarvan kunt u toegang krijgen tot uw gegevens met behulp van een Azure ML- [gegevensset](how-to-train-with-datasets.md).

### <a name="what-happens-during-run-execution"></a>Wat er gebeurt tijdens de uitvoering van het programma
Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen door lopen:

- **Voorbereiden**: een docker-installatie kopie wordt gemaakt volgens de gedefinieerde omgeving. De afbeelding wordt geüpload naar het container register van de werk ruimte en opgeslagen in de cache voor latere uitvoeringen. Logboeken worden ook gestreamd naar de uitvoerings geschiedenis en kunnen worden weer gegeven om de voortgang te bewaken. Als er in plaats daarvan een gecuratorde omgeving wordt opgegeven, wordt er een back-up van de in de cache opgeslagen installatie kopie gebruikt.

- **Schalen**: het cluster probeert omhoog te schalen als het batch AI-cluster meer knoop punten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren**: alle scripts in de map script worden geüpload naar het Compute-doel, gegevens archieven worden gekoppeld of gekopieerd en de `script` wordt uitgevoerd. Uitvoer van stdout en de map **./logs** worden gestreamd naar de uitvoerings geschiedenis en kunnen worden gebruikt om de uitvoering te bewaken.

- **Na de verwerking**: de map **./outputs** van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis.

## <a name="save-and-register-the-model"></a>Het model opslaan en registreren

Zodra u het model hebt getraind, kunt u het opslaan en registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

Voeg de volgende code toe aan het trainings script, train_iris. py, om het model op te slaan. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registreer het model in uw werk ruimte met de volgende code. Door de para meters op te geven, en wordt de `model_framework` `model_framework_version` implementatie van `resource_configuration` geen code model beschikbaar. Met de implementatie van geen code model kunt u uw model rechtstreeks implementeren als een webservice vanuit het geregistreerde model [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) . het object definieert de reken resource voor de webservice.

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

Het model dat u zojuist hebt geregistreerd, kan op dezelfde manier worden geïmplementeerd als andere geregistreerde modellen in azure ML. De implementatie-instructie bevat een sectie over het registreren van modellen, maar u kunt direct door gaan naar het [maken van een reken doel](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

### <a name="preview-no-code-model-deployment"></a>Evaluatie Implementatie van geen code model

In plaats van de traditionele implementatie route kunt u ook de functie voor het implementeren van geen code (preview) gebruiken voor scikit-learn. Implementatie van geen code model wordt ondersteund voor alle ingebouwde scikit-informatie over model typen. Als u het model registreert zoals hierboven wordt weer gegeven, `model_framework` `model_framework_version` `resource_configuration` kunt u gewoon de [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statische functie gebruiken om uw model te implementeren.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Opmerking: deze afhankelijkheden zijn opgenomen in de vooraf gemaakte scikit.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

De volledige [instructies voor](how-to-deploy-and-where.md) het implementeren van een implementatie in azure machine learning meer dieper.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een scikit-leer model getraind en geregistreerd en hebt u geleerd over implementatie opties. Raadpleeg de volgende artikelen voor meer informatie over Azure Machine Learning.

* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
