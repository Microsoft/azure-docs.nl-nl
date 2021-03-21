---
title: MLflow bijhouden van ML-experimenten
titleSuffix: Azure Machine Learning
description: Stel MLflow met Azure Machine Learning in voor het vastleggen van metrische gegevens en artefacten uit ML-modellen en implementeer uw ML-modellen als een webservice.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 02684ba91c207357e15684870a6fa0ceab3e17ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520962"
---
# <a name="train-and-track-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Conversieer en houd ML modellen met MLflow en Azure Machine Learning (preview)

In dit artikel leert u hoe u de tracerings-URI en logboek registratie-API van MLflow kunt inschakelen, die gezamenlijk bekend staat als [MLflow-tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), om Azure machine learning te verbinden als de back-end van uw MLflow experimenten. 

Ondersteunde mogelijkheden zijn onder andere: 

+ Meet en log-gegevens en artefacten in uw [Azure machine learning-werk ruimte](./concept-azure-machine-learning-architecture.md#workspace)bijhouden. Als u MLflow tracking al gebruikt voor uw experimenten, biedt de werk ruimte een gecentraliseerde, veilige en schaal bare locatie voor het opslaan van metrische gegevens en modellen voor trainingen.

+ Verzend trainings taken met [MLflow-projecten](https://www.mlflow.org/docs/latest/projects.html) met Azure machine learning back-end-ondersteuning (preview). U kunt taken lokaal verzenden met Azure Machine Learning tracking of uw uitvoeringen migreren naar de Cloud, zoals via een [Azure machine learning reken kracht](./how-to-create-attach-compute-cluster.md).

+ Modellen in MLflow en Azure Machine Learning model register bijhouden en beheren.

[MLflow](https://www.mlflow.org) is een open-source bibliotheek voor het beheren van de levens cyclus van uw machine learning experimenten. MLFlow tracking is een onderdeel van MLflow waarmee u de metrische gegevens en model artefacten van uw training kunt vastleggen en bijhouden, ongeacht de omgeving van uw experiment, lokaal op uw computer, op een extern Compute-doel, een virtuele machine of een [Azure Databricks cluster](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> MLflow is een open-source bibliotheek die regel matig wordt gewijzigd. Als zodanig moeten de functies die beschikbaar zijn via de Azure Machine Learning-en MLflow-integratie, worden beschouwd als een preview en niet volledig worden ondersteund door micro soft.

In het volgende diagram ziet u hoe u met MLflow tracking de metrische gegevens van een experiment kunt bijhouden en model artefacten kunt opslaan in uw Azure Machine Learning-werk ruimte.

![mlflow met Azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevens wetenschappers en ontwikkel aars die het model trainings proces willen bewaken. Zie [Azure machine learning bewaken](monitor-azure-machine-learning.md)als u een beheerder bent die geïnteresseerd is in het bewaken van het resource gebruik en gebeurtenissen van Azure machine learning, zoals quota's, voltooide trainings uitvoeringen of voltooide implementaties van modellen.

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow-en Azure Machine Learning-clients vergelijken

 De volgende tabel bevat een overzicht van de verschillende clients die Azure Machine Learning kunnen gebruiken en hun respectieve functie mogelijkheden.

 MLflow tracking biedt metrische logboek registratie en opslag functies voor artefacten die alleen beschikbaar zijn via de [python-SDK van Azure machine learning](/python/api/overview/azure/ml/intro).

| Mogelijkheid | MLflow tracking &-implementatie | Azure Machine Learning python-SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Werk ruimte beheren |   | ✓ | ✓ | ✓ |
| Gegevens archieven gebruiken  |   | ✓ | ✓ | |
| Metrische logboekgegevens      | ✓ | ✓ |   | |
| Artefacten uploaden | ✓ | ✓ |   | |
| Metrische gegevens bekijken     | ✓ | ✓ | ✓ | ✓ |
| Compute beheren   |   | ✓ | ✓ | ✓ |
| Modellen implementeren    | ✓ | ✓ | ✓ | ✓ |
|Model prestaties bewaken||✓|  |   |
| Gegevensafwijkingen detecteren |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Vereisten

* Installeer het `azureml-mlflow`-pakket. 
    * Dit pakket maakt automatisch deel uit `azureml-core` van de [Azure machine learning PYTHON-SDK](/python/api/overview/azure/ml/install), waarmee de connectiviteit voor MLflow wordt geboden om toegang te krijgen tot uw werk ruimte.
* [Maak een Azure machine learning-werkruimte](how-to-manage-workspace.md).
    * Bekijk welke [toegangs machtigingen u nodig hebt om uw MLflow-bewerkingen uit te voeren met uw werk ruimte](how-to-assign-roles.md#mlflow-operations).

## <a name="track-local-runs"></a>Lokale uitvoeringen bijhouden

MLflow tracking met Azure Machine Learning kunt u de vastgelegde metrische gegevens en artefacten van uw lokale uitvoeringen opslaan in uw Azure Machine Learning-werk ruimte.

Importeer de `mlflow` klassen en en [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) Configureer de MLflow om uw werk ruimte te openen.

In de volgende code wijst de `get_mlflow_tracking_uri()` methode een uniek tracerings-URI-adres toe aan de werk ruimte `ws` en `set_tracking_uri()` wijst de tracerings-URI van de MLflow naar dat adres.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>De tracerings-URI is Maxi maal een uur of minder geldig. Als u het script opnieuw opstart na enige inactieve tijd, gebruikt u de get_mlflow_tracking_uri-API om een nieuwe URI op te halen.

Stel de naam van het MLflow-experiment in met `set_experiment()` en start uw trainings uitvoering met `start_run()` . Vervolgens gebruikt `log_metric()` u de MLflow-logboek registratie-API en begint u met het vastleggen van metrische gegevens voor uw trainings uitvoering.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Externe uitvoeringen bijhouden

Met extern uitvoeren kunt u uw modellen trainen op krachtige reken mogelijkheden, zoals virtuele machines met GPU of Machine Learning Compute clusters. Zie [Compute-doelen voor model training gebruiken](how-to-set-up-training-targets.md) voor meer informatie over verschillende reken opties.

MLflow tracking met Azure Machine Learning kunt u de vastgelegde metrische gegevens en artefacten opslaan vanaf uw externe uitvoeringen in uw Azure Machine Learning-werk ruimte. Bij elke uitvoering met de tracerings code MLflow worden metrische gegevens automatisch geregistreerd in de werk ruimte. 

De volgende voor beeld-Conda-omgeving bevat `mlflow` en `azureml-mlflow` als PIP-pakketten. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

Configureer uw reken-en trainings uitvoerings omgeving in uw script met de- [`Environment`](/python/api/azureml-core/azureml.core.environment.environment) klasse. Maak vervolgens  [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) met uw externe Compute als het reken doel.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Met deze Compute-en training-uitvoerings configuratie gebruikt `Experiment.submit()` u de methode voor het verzenden van een run. Met deze methode wordt de tracerings-URI van de MLflow automatisch ingesteld en wordt de logboek registratie van MLflow naar uw werk ruimte omgeleid.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Train met MLflow-projecten

Met [MLflow-projecten](https://mlflow.org/docs/latest/projects.html) kunt u uw code indelen en beschrijven om andere gegevens wetenschappers (of automatische hulpprogram ma's) te kunnen uitvoeren. Met MLflow-projecten met Azure Machine Learning kunt u uw trainings uitvoeringen in uw werk ruimte bijhouden en beheren. 

In dit voor beeld ziet u hoe u MLflow-projecten lokaal verzendt met Azure Machine Learning tracking.

Installeer het `azureml-mlflow` pakket om MLflow tracking te gebruiken met Azure machine learning op uw experimenten. Uw experimenten kunnen worden uitgevoerd via een Jupyter Notebook of code-editor.

```shell
pip install azureml-mlflow
```

Importeer de `mlflow` klassen en en [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) Configureer de MLflow om uw werk ruimte te openen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Stel de naam van het MLflow-experiment in met `set_experiment()` en start uw trainings uitvoering met `start_run()` . Gebruik vervolgens `log_metric()` om de MLflow-logboek registratie-API te activeren en de metrische gegevens van uw trainings uitvoering te registreren.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Maak het back-end-configuratie object om de benodigde informatie voor de integratie op te slaan, zoals het berekenings doel en het type beheerde omgeving dat moet worden gebruikt.

```python
backend_config = {"USE_CONDA": False}
```
Voeg het `azureml-mlflow` pakket toe als een PIP-afhankelijkheid voor het configuratie bestand van uw omgeving om metrische gegevens en sleutel artefacten in uw werk ruimte bij te houden. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Verzend de lokale uitvoering en controleer of u de para meter hebt ingesteld `backend = "azureml" ` . Met deze instelling kunt u lokaal uitvoeringen verzenden en de extra ondersteuning voor automatische uitvoer tracering, logboek bestanden, moment opnamen en afdruk fouten ophalen in uw werk ruimte. 

Bekijk uw uitvoeringen en metrische gegevens in de [Azure machine learning Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Metrische gegevens en artefacten in uw werk ruimte weer geven

De metrische gegevens en artefacten van MLflow-logboek registratie worden bewaard in uw werk ruimte. Als u ze op een wille keurig tijdstip wilt weer geven, gaat u naar uw werk ruimte en zoekt u het experiment op naam in uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).  Of voer de onderstaande code uit. 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Modellen beheren 

Registreer en volg uw modellen met het [Azure machine learning model register](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) dat het REGI ster van het MLflow-model ondersteunt. Azure Machine Learning modellen zijn afgestemd op het MLflow-model schema, waardoor deze modellen eenvoudig kunnen worden geëxporteerd en geïmporteerd in verschillende werk stromen. De MLflow-gerelateerde meta gegevens zoals, run-ID wordt ook gelabeld met het geregistreerde model voor traceer baarheid. Gebruikers kunnen trainings runs verzenden, registreren en implementeren die zijn geproduceerd vanuit MLflow-uitvoeringen. 

Als u uw productie gereed model in één stap wilt implementeren en registreren, raadpleegt u [MLflow modellen implementeren en registreren](how-to-deploy-mlflow-models.md).

Voer de volgende stappen uit om een model van een uitvoering te registreren en weer te geven:

1. Als de uitvoering is voltooid, roept u de- `register_model()` methode aan.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Bekijk het geregistreerde model in uw werk ruimte met [Azure machine learning Studio](overview-what-is-machine-learning-studio.md).

    In het volgende voor beeld heeft het geregistreerde model de `my-model` MLflow traceren van meta gegevens. 

    ![REGI ster-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Selecteer het tabblad **artefacten** om alle model bestanden te zien die zijn afgestemd op het MLflow-model schema (Conda. yaml, MLmodel, model. PKL).

    ![model-schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Selecteer MLmodel om het MLmodel-bestand weer te geven dat is gegenereerd door de uitvoering.

    ![MLmodel-schema](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om de vastgelegde metrische gegevens en artefacten in uw werk ruimte te gebruiken, is de mogelijkheid om ze afzonderlijk te verwijderen, momenteel niet beschikbaar. Verwijder in plaats daarvan de resource groep die het opslag account en de werk ruimte bevat, zodat u geen kosten in rekening brengt:

1. Selecteer **Resourcegroepen** links in Azure Portal.

   ![Verwijderen in Azure Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Selecteer de resourcegroep die u eerder hebt gemaakt uit de lijst.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.

## <a name="example-notebooks"></a>Voorbeeldnotebooks

De [MLflow met Azure ml-notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstreren en uitvouwen op concepten die in dit artikel worden gepresenteerd.

> [!NOTE]
> Een door de Community aangedreven opslag plaats van voor beelden met behulp van mlflow vindt u op https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Volgende stappen

* [Implementeer modellen met MLflow](how-to-deploy-mlflow-models.md).
* Bewaak uw productie modellen voor [gegevens drift](./how-to-enable-data-collection.md).
* [Volg Azure Databricks uitvoeringen met MLflow](how-to-use-mlflow-azure-databricks.md).
* [Uw modellen beheren](concept-model-management-and-deployment.md).