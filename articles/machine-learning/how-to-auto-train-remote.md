---
title: Automated ML externe Compute-doelen
titleSuffix: Azure Machine Learning
description: Meer informatie over het bouwen van modellen met behulp van geautomatiseerde machine learning op een Azure Machine Learning extern Compute-doel met Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.custom: how-to, automl
ms.date: 03/09/2020
ms.openlocfilehash: 402af581e0ca6de9936b78106840a4fe273069d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503450"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Modellen trainen met geautomatiseerde machine learning in de cloud



In Azure Machine Learning traint u uw model op verschillende soorten reken bronnen die u beheert. Het Compute-doel kan een lokale computer of een bron in de Cloud zijn.

U kunt uw machine learning experiment eenvoudig opschalen of uitschalen door extra reken doelen toe te voegen, zoals Azure Machine Learning Compute (AmlCompute). AmlCompute is een infrastructuur voor beheerde berekeningen waarmee u eenvoudig een berekening met één of meerdere knooppunten kunt maken.

In dit artikel leert u hoe u een model bouwt met behulp van geautomatiseerde MILLILITERs met AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Hoe verschilt extern van lokaal?

Er zijn meer functies beschikbaar wanneer u een extern Compute-doel gebruikt.  Zie [lokale en externe Compute-doelen](concept-automated-ml.md#local-remote)voor meer informatie.

In de zelf studie "[een classificatie model trainen met geautomatiseerde machine learning](tutorial-auto-train-models.md)" leert u hoe u een lokale computer kunt gebruiken om een model met automatische ml te trainen. De werk stroom wanneer training lokaal ook van toepassing is op externe doelen. Als u op afstand wilt trainen, maakt u eerst een extern Compute-doel zoals AmlCompute. Vervolgens configureert u de externe resource en verzendt u de code daar.

In dit artikel worden de extra stappen beschreven die nodig zijn voor het uitvoeren van een geautomatiseerd experiment op een extern AmlCompute-doel. Het werkruimte object, `ws` , in de zelf studie, wordt in de hele code gebruikt.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Bron maken

Maak het [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29) doel in uw werk ruimte ( `ws` ) als deze nog niet bestaat.

**Geschatte tijd**: het maken van het AmlCompute-doel duurt ongeveer 5 minuten.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

U kunt nu het `compute_target` object gebruiken als het externe Compute-doel.

De beperkingen voor de cluster naam zijn onder andere:
+ Moet korter zijn dan 64 tekens.
+ Kan geen van de volgende tekens bevatten: `\` ~! @ # $% ^ & * () = + _ [] {} \\ \\ |;: \' \\ ",  < > /?. `

## <a name="access-data-using-tabulardataset-function"></a>Toegang tot gegevens met de functie TabularDataset

Gedefinieerd training_data als [`TabularDataset`](/python/api/azureml-core/azureml.data.tabulardataset) en het label, dat wordt door gegeven aan automatische milliliters in de [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) . De `TabularDataset` methode `from_delimited_files` stelt de standaard waarde `infer_column_types` in op waar, waardoor het kolommen type automatisch wordt afleiden. 

Als u de kolom typen hand matig wilt instellen, kunt u het argument instellen `set_column_types` om het type van elke kolom hand matig in te stellen. In het volgende code voorbeeld zijn de gegevens afkomstig uit het sklearn-pakket.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>Experiment configureren
Geef de instellingen voor op `AutoMLConfig` .  (Zie een [volledige lijst met para meters](how-to-configure-auto-train.md#configure-experiment) en de bijbehorende mogelijke waarden.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Trainings experiment verzenden

Verzend de configuratie nu om automatisch het algoritme te selecteren, de Hyper-para meters en het model te trainen.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'Tutorial-automl-remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

De uitvoer is vergelijkbaar met de uitvoer in het volgende voorbeeld:

```output
Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
***********************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE:  A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
***********************************************************************************************

 ITERATION     PIPELINE                               DURATION                METRIC      BEST
         2      Standardize SGD classifier            0:02:36                  0.954     0.954
         7      Normalizer DT                         0:02:22                  0.161     0.954
         0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
         4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
         1      Normalizer kNN                        0:02:44                  0.984     0.984
         9      Normalizer extra trees                0:03:15                  0.834     0.984
         5      Robust Scaler DT                      0:02:18                  0.736     0.984
         8      Standardize kNN                       0:02:05                  0.981     0.984
         6      Standardize SVM                       0:02:18                  0.984     0.984
        10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
        11      Standardize SGD classifier            0:02:24                  0.863     0.984
         3      Standardize gradient boosting         0:03:03                  0.971     0.984
        12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
        14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
        13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
        15      Robust Scaler kNN                     0:02:28                  0.904     0.989
        17      Standardize kNN                       0:02:22                  0.974     0.989
        16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
        18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
        19      Robust Scaler kNN                     0:02:32                  0.983     0.989
```

## <a name="explore-results"></a>Resultaten verkennen

U kunt dezelfde Jupyter- [widget](/python/api/azureml-widgets/azureml.widgets) gebruiken zoals weer gegeven in [de trainings zelfstudie](tutorial-auto-train-models.md#explore-the-results) om een grafiek en een tabel met resultaten weer te geven.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Hier ziet u een statische afbeelding van de widget.  In het notitie blok kunt u klikken op een wille keurige regel in de tabel om uitvoerings eigenschappen en uitvoer logboeken voor die uitvoering weer te geven.   U kunt ook de vervolg keuzelijst boven de grafiek gebruiken om een grafiek van elke beschik bare metrische gegevens voor elke iteratie weer te geven.

![tabel van widget](./media/how-to-auto-train-remote/table.png)
![grafiek van widget](./media/how-to-auto-train-remote/plot.png)

Met de widget wordt een URL weer gegeven die u kunt gebruiken om de afzonderlijke details van de uitvoering te bekijken en te verkennen.  

Als u zich niet in een Jupyter-notebook bevindt, kunt u de URL van de uitvoeringsrun zelf weer geven:

```
remote_run.get_portal_url()
```

Dezelfde informatie is beschikbaar in uw werk ruimte.  Zie voor meer informatie over deze resultaten [geautomatiseerd machine learning resultaten evalueren](how-to-understand-automated-ml.md).

## <a name="example"></a>Voorbeeld

In het volgende [notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) worden concepten in dit artikel gedemonstreerd.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het configureren van instellingen voor automatische training](how-to-configure-auto-train.md).
* Bekijk de [functie voor het inschakelen](how-to-machine-learning-interpretability-automl.md) van model interpret functies in geautomatiseerde ml experimenten.