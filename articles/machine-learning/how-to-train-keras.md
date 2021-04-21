---
title: Deep Learning Keras-modellen trainen
titleSuffix: Azure Machine Learning
description: Leer hoe u een Deep Neural Network-classificatiemodel van Keras traint en registreert dat wordt uitgevoerd op TensorFlow met behulp van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 555ec90bbd73cee401f6f35aa04598792d2f24f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817142"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Keras-modellen op schaal trainen met Azure Machine Learning

In dit artikel leert u hoe u uw Keras-trainingsscripts kunt uitvoeren met Azure Machine Learning.

De voorbeeldcode in dit artikel laat zien hoe u een Keras-classificatiemodel traint en registreert dat is gebouwd met behulp van de TensorFlow-back-Azure Machine Learning. Het maakt gebruik van de populaire [MNIST-gegevensset](http://yann.lecun.com/exdb/mnist/) voor het classificeren van handgeschreven cijfers met behulp van een Deep Neural Network (DNN) dat is gebouwd met behulp van de [Keras Python-bibliotheek](https://keras.io) die wordt uitgevoerd boven op [TensorFlow.](https://www.tensorflow.org/overview)

Keras is een neurale netwerk-API op hoog niveau die kan worden uitgevoerd boven op andere populaire DNN-frameworks om de ontwikkeling te vereenvoudigen. Met Azure Machine Learning kunt u snel trainingstaken uitschalen met behulp van elastische cloudrekenbronnen. U kunt ook uw trainings- en versiemodellen bijhouden, modellen implementeren en nog veel meer.

Of u nu een Keras-model vanaf de basis ontwikkelt of een bestaand model naar de cloud brengt, Azure Machine Learning kan u helpen bij het bouwen van modellen die gereed zijn voor productie.

> [!NOTE]
> Als u de Keras API **tf.keras** gebruikt die is ingebouwd in TensorFlow en niet het zelfstandige Keras-pakket, raadpleegt u in plaats daarvan [TensorFlow-modellen trainen.](how-to-train-tensorflow.md)

## <a name="prerequisites"></a>Vereisten

Voer deze code uit op een van deze omgevingen:

- Azure Machine Learning rekenproces : er zijn geen downloads of installatie nodig

     - Voltooi de [zelfstudie: Omgeving en werkruimte instellen om](tutorial-1st-experiment-sdk-setup.md) een toegewezen notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de map met voorbeelden op de notebookserver een voltooid en uitgebreid notebook door te navigeren naar deze map: **how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras** folder.

 - Uw eigen Jupyter Notebook server

    - [Installeer de Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Maak een configuratiebestand voor de werkruimte.](how-to-configure-environment.md#workspace)
    - [De voorbeeldscriptbestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` En `utils.py`

    U kunt ook een voltooide Jupyter Notebook [van](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) deze handleiding vinden op de pagina met GitHub-voorbeelden. Het notebook bevat uitgebreide secties over intelligente hyperparameterafstemming, modelimplementatie en notebookwidgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie stelt u het trainingsexperiment in door de vereiste Python-pakketten te laden, een werkruimte te initialiseren, de FileDataset voor de invoertrainingsgegevens te maken, het rekendoel te maken en de trainingsomgeving te definiëren.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde Python-bibliotheken.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning is](concept-workspace.md) de resource op het hoogste niveau voor de service. Het biedt u een centrale plaats om te werken met alle artefacten die u maakt. In de Python-SDK hebt u toegang tot de werkruimteartefacten door een -object te [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) maken.

Maak een werkruimteobject op basis van `config.json` het bestand dat is gemaakt in de sectie met [vereisten.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Een bestandsset maken

Een `FileDataset` object verwijst naar een of meer bestanden in de gegevensstore van uw werkruimte of openbare URL's. De bestanden kunnen elke indeling hebben en de klasse biedt u de mogelijkheid om de bestanden te downloaden of aan uw rekenkracht te monteren. Door een te `FileDataset` maken, maakt u een verwijzing naar de locatie van de gegevensbron. Als u transformaties op de gegevensset hebt toegepast, worden deze ook opgeslagen in de gegevensset. De gegevens blijven bewaard op de bestaande locatie, dus maakt u geen extra opslagkosten. Zie de [handleiding voor](./how-to-create-register-datasets.md) het pakket voor `Dataset` meer informatie.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

U kunt de methode gebruiken om de gegevensset te registreren bij uw werkruimte, zodat deze kan worden gedeeld met anderen, opnieuw kan worden gebruikt in verschillende experimenten en door de naam kan worden verwezen in uw `register()` trainingsscript.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Een rekendoel maken

Maak een rekendoel voor uw trainings job om op uit te voeren. In dit voorbeeld maakt u een GPU-Azure Machine Learning rekencluster.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Zie het artikel Wat is een rekendoel? voor meer informatie over [rekendoelen.](concept-compute-target.md)

### <a name="define-your-environment"></a>Uw omgeving definiëren

Definieer de Azure [ML-omgeving](concept-environments.md) die de afhankelijkheden van uw trainingsscript inkapseld.

Definieer eerst uw Conda-afhankelijkheden in een YAML-bestand; In dit voorbeeld heeft het bestand de naam `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Maak een Azure ML-omgeving op deze conda-omgevingsspecificatie. De omgeving wordt tijdens runtime verpakt in een Docker-container.

Als er geen basisafbeelding is opgegeven, gebruikt Azure ML standaard een CPU-afbeelding `azureml.core.environment.DEFAULT_CPU_IMAGE` als basisafbeelding. Omdat in dit voorbeeld training wordt uitgevoerd op een GPU-cluster, moet u een GPU-basisafbeelding opgeven met de benodigde GPU-stuurprogramma's en -afhankelijkheden. Azure ML onderhoudt een set basisafbeeldingen die zijn gepubliceerd op Microsoft Container Registry (MCR) die u kunt gebruiken. Zie de [GitHub-opslagplaats Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) voor meer informatie.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Zie Softwareomgevingen maken en gebruiken in Azure Machine Learning voor meer informatie over het maken [en gebruiken van omgevingen.](how-to-use-environments.md)

## <a name="configure-and-submit-your-training-run"></a>Uw trainingsrun configureren en verzenden

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken
Haal eerst de gegevens op uit de gegevensstore van de werkruimte met behulp van de `Dataset` klasse .

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Maak een ScriptRunConfig-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren.

Alle argumenten voor uw trainingsscript worden doorgegeven via de opdrachtregel als deze is opgegeven in de `arguments` parameter . De DatasetConsumptionConfig voor onze FileDataset wordt als argument doorgegeven aan het trainingsscript voor het `--data-folder` argument . Azure ML zet deze DatasetConsumptionConfig om naar het bevestigingspunt van de back-datastore, die vervolgens vanuit het trainingsscript kan worden gebruikt.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Zie Configure [and submit training runs](how-to-set-up-training-targets.md)(Trainingsuit runs configureren en verzenden) voor meer informatie over het configureren van taken met ScriptRunConfig.

> [!WARNING]
> Als u eerder de TensorFlow-estimator hebt gebruikt om uw Keras-trainingstaken te configureren, moet u er rekening mee houden dat estimators zijn afgeschaft vanaf de 1.19.0 SDK-release. Met Azure ML SDK >= 1.15.0 is ScriptRunConfig de aanbevolen manier om trainingstaken te configureren, inclusief de taken die gebruikmaken van frameworks voor deep learning. Zie de migratiehandleiding [Estimator to ScriptRunConfig voor](how-to-migrate-from-estimators-to-scriptrunconfig.md)veelvoorkomende migratievragen.

### <a name="submit-your-run"></a>Uw run verzenden

Het [object Run](/python/api/azureml-core/azureml.core.run%28class%29) biedt de interface voor de run history terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Wat gebeurt er tijdens de uitvoering
Wanneer de uitvoering wordt uitgevoerd, doorloop deze de volgende fasen:

- **Voorbereiden:** er wordt een Docker-afbeelding gemaakt op basis van de omgeving die is gedefinieerd. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere runs. Logboeken worden ook gestreamd naar de uitvoeringsgeschiedenis en kunnen worden bekeken om de voortgang te controleren. Als in plaats daarvan een gecureerde omgeving wordt opgegeven, wordt de back-back-van die gecureerde omgeving in de cache gebruikt.

- **Schalen:** het cluster probeert omhoog te schalen als het Batch AI cluster meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren:** alle scripts in de scriptmap worden geüpload naar het rekendoel, gegevensopslag worden aan of gekopieerd en de `script` wordt uitgevoerd. Uitvoer van stdout en de **map ./logs** worden gestreamd naar de uitvoergeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Naverwerking:** de **map ./outputs** van de run wordt gekopieerd naar de uitvoergeschiedenis.

## <a name="register-the-model"></a>Het model registreren

Zodra u het model hebt getraind, kunt u het registreren bij uw werkruimte. Met modelregistratie kunt u uw modellen opslaan en versiebeheeren in uw werkruimte om [het modelbeheer en de implementatie te vereenvoudigen.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> De implementatie-how-to bevat een sectie over het registreren [](how-to-deploy-and-where.md#choose-a-compute-target) van modellen, maar u kunt direct verder gaan met het maken van een rekendoel voor implementatie, omdat u al een geregistreerd model hebt.

U kunt ook een lokale kopie van het model downloaden. Dit kan handig zijn voor het lokaal uitvoeren van aanvullende modelvalidatiewerkzaamheden. In het trainingsscript, , wordt het model met een TensorFlow-opslagobject opgeslagen in een lokale map `keras_mnist.py` (lokaal voor het rekendoel). U kunt het run-object gebruiken om een kopie uit de run history te downloaden.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Keras-model getraind en geregistreerd op Azure Machine Learning. Als u wilt weten hoe u een model implementeert, gaat u verder met ons artikel over modelimplementatie.

* [Hoe en waar u modellen implementeert](how-to-deploy-and-where.md)
* [Metrische gegevens van de run bijhouden tijdens de training](how-to-log-view-metrics.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentiearchitectuur voor gedistribueerde deep learning-training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
