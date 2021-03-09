---
title: Keras-modellen voor diepe Learning trainen
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen en registreren van een Keras diepe Neural-netwerk classificatie model dat wordt uitgevoerd op tensor flow met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2b4af9dec2bf397ad2766c68d547eeac85a9a9a3
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518361"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Keras-modellen op schaal trainen met Azure Machine Learning

In dit artikel leert u hoe u uw Keras-trainings scripts kunt uitvoeren met Azure Machine Learning.

De voorbeeld code in dit artikel laat zien hoe u een Keras-classificatie model traint en registreert dat is gemaakt met behulp van de tensor flow-back-end met Azure Machine Learning. Er wordt gebruikgemaakt van de populaire [MNIST-gegevensset](http://yann.lecun.com/exdb/mnist/) om handgeschreven cijfers te classificeren met behulp van een diepe Neural Network (DNN) die is gebouwd met behulp van de [Keras python-bibliotheek](https://keras.io) op [tensor flow](https://www.tensorflow.org/overview).

Keras is een Neural-netwerk-API op hoog niveau die de ontwikkeling van andere populaire DNN-Frameworks kan vereenvoudigen. Met Azure Machine Learning kunt u trainings taken snel uitschalen met behulp van elastische Cloud Compute-resources. U kunt ook uw trainings uitvoeringen, versie modellen, implementatie modellen en nog veel meer volgen.

Of u nu een Keras-model ontwikkelt of een bestaand model in de Cloud brengt, Azure Machine Learning u kunt helpen bij het bouwen van productie klare modellen.

> [!NOTE]
> Als u de Keras API **tf. Keras** die is ingebouwd in tensor flow en niet het zelfstandige Keras-pakket gebruikt, raadpleegt u in plaats daarvan [tensor flow-modellen trainen](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van de volgende omgevingen:

- Azure Machine Learning Compute-instantie-geen down loads of installatie vereist

     - Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.
    - Zoek in de map met voor beelden op de notebook server een volledig en uitgebreid notitie blok door naar deze map te navigeren: **How-to-use-azureml > ml-frameworks > keras > Train-afstemming-Tune-Deploy-with-Keras** .

 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure machine learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
    - [De voorbeeld script bestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` maar `utils.py`

    U kunt ook een voltooide [Jupyter notebook versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) van deze hand leiding vinden op de pagina met voor beelden van github. Het notitie blok bevat uitgebreide secties die betrekking hebben op intelligent afstemming tuning, model implementatie en notebook widgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainings experiment opgesteld door het laden van de vereiste Python-pakketten, het initialiseren van een werk ruimte, het maken van de FileDataset voor de gegevens van de invoer training, het maken van het reken doel en het definiëren van de trainings omgeving.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde python-bibliotheken.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) object te maken.

Maak een werkruimte object op basis van het `config.json` bestand dat in de [sectie vereisten](#prerequisites)is gemaakt.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Een bestands gegevensset maken

Een- `FileDataset` object verwijst naar een of meer bestanden in uw werk ruimte of open bare url's. De bestanden hebben een wille keurige indeling en de-klasse biedt u de mogelijkheid om de bestanden te downloaden of te koppelen aan uw computer. Door een te maken `FileDataset` , maakt u een verwijzing naar de locatie van de gegevens bron. Als u trans formaties hebt toegepast op de gegevensset, worden deze ook opgeslagen in de gegevens verzameling. De gegevens blijven bewaard op de bestaande locatie, dus maakt u geen extra opslagkosten. Raadpleeg de [hand leiding van het](./how-to-create-register-datasets.md) `Dataset` pakket voor meer informatie.

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

U kunt de- `register()` methode gebruiken om de gegevensset te registreren in uw werk ruimte, zodat ze kunnen worden gedeeld met anderen, opnieuw worden gebruikt in verschillende experimenten en waarnaar wordt verwezen met de naam in uw trainings script.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Een rekendoel maken

Maak een compute-doel voor uw trainings taak om uit te voeren. In dit voor beeld maakt u een Azure Machine Learning Compute-cluster waarvoor GPU is ingeschakeld.

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

Zie voor meer informatie over Compute-doelen het artikel [Wat is een reken doel](concept-compute-target.md) .

### <a name="define-your-environment"></a>Uw omgeving definiëren

Definieer de Azure ML- [omgeving](concept-environments.md) waarin de afhankelijkheden van uw trainings script worden ingekapseld.

Definieer eerst uw Conda-afhankelijkheden in een YAML-bestand. in dit voor beeld heeft het bestand de naam `conda_dependencies.yml` .

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

Een Azure ML-omgeving maken op basis van deze Conda Environment-specificatie. De omgeving wordt tijdens runtime ingepakt in een docker-container.

Als er geen basis installatie kopie is opgegeven, wordt door Azure ML standaard een CPU-installatie kopie gebruikt `azureml.core.environment.DEFAULT_CPU_IMAGE` als basis installatie kopie. Omdat in dit voor beeld training wordt uitgevoerd op een GPU-cluster, moet u een GPU-basis installatie kopie opgeven met de benodigde GPU-Stuur Programma's en-afhankelijkheden. Azure ML onderhoudt een set basis installatie kopieën die zijn gepubliceerd op micro soft Container Registry (MCR) die u kunt gebruiken. Zie de [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) github opslag plaats voor meer informatie.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Zie [software omgevingen maken en gebruiken in azure machine learning](how-to-use-environments.md)voor meer informatie over het maken van en het gebruik van omgevingen.

## <a name="configure-and-submit-your-training-run"></a>Uw trainings uitvoering configureren en verzenden

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken
Haal eerst de gegevens op uit de werk ruimte Data Store met behulp van de- `Dataset` klasse.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Maak een ScriptRunConfig-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren.

Eventuele argumenten voor uw trainings script worden door gegeven via de opdracht regel indien opgegeven in de `arguments` para meter. De DatasetConsumptionConfig voor onze FileDataset wordt door gegeven als een argument voor het-trainings script, voor het `--data-folder` argument. Met Azure ML wordt deze DatasetConsumptionConfig omgezet naar het koppel punt van de back-upopslag, die vervolgens kan worden geopend vanuit het trainings script.

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

Zie [trainings uitvoeringen configureren en verzenden](how-to-set-up-training-targets.md)voor meer informatie over het configureren van taken met ScriptRunConfig.

> [!WARNING]
> Als u eerder de tensor flow Estimator hebt gebruikt voor het configureren van uw Keras-trainings taken, moet u er rekening mee houden dat de schattingen zijn afgeschaft vanaf de 1.19.0 SDK-release. Met Azure ML SDK >= 1.15.0 is ScriptRunConfig de aanbevolen manier om trainings taken te configureren, inclusief de functies die gebruikmaken van diepe lessen. Zie voor algemene vragen over migratie de [migratie handleiding voor Estimator naar ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-your-run"></a>Uw uitvoering verzenden

Het [object run](/python/api/azureml-core/azureml.core.run%28class%29) biedt de interface voor de uitvoerings geschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Wat er gebeurt tijdens de uitvoering van het programma
Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen door lopen:

- **Voorbereiden**: een docker-installatie kopie wordt gemaakt volgens de gedefinieerde omgeving. De afbeelding wordt geüpload naar het container register van de werk ruimte en opgeslagen in de cache voor latere uitvoeringen. Logboeken worden ook gestreamd naar de uitvoerings geschiedenis en kunnen worden weer gegeven om de voortgang te bewaken. Als er in plaats daarvan een gecuratorde omgeving wordt opgegeven, wordt er een back-up van de in de cache opgeslagen installatie kopie gebruikt.

- **Schalen**: het cluster probeert omhoog te schalen als het batch AI-cluster meer knoop punten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren**: alle scripts in de map script worden geüpload naar het Compute-doel, gegevens archieven worden gekoppeld of gekopieerd en de `script` wordt uitgevoerd. Uitvoer van stdout en de map **./logs** worden gestreamd naar de uitvoerings geschiedenis en kunnen worden gebruikt om de uitvoering te bewaken.

- **Na de verwerking**: de map **./outputs** van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis.

## <a name="register-the-model"></a>Het model registreren

Zodra u het model hebt getraind, kunt u het registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> De implementatie-instructie bevat een sectie over het registreren van modellen, maar u kunt direct door gaan naar het [maken van een reken doel](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

U kunt ook een lokale kopie van het model downloaden. Dit kan handig zijn om een extra model validatie lokaal te kunnen uitvoeren. In het trainings script wordt `keras_mnist.py` het model door een tensor flow-beveiligings object naar een lokale map (lokaal naar het Compute-doel) bewaard. U kunt het object run gebruiken om een kopie te downloaden uit de uitvoerings geschiedenis.

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

In dit artikel hebt u een Keras-model getraind en geregistreerd op Azure Machine Learning. Ga verder met ons model implementatie artikel voor meer informatie over het implementeren van een model.

* [Hoe en waar modellen moeten worden geïmplementeerd](how-to-deploy-and-where.md)
* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentie architectuur voor gedistribueerde training van diep gaande lessen in azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
