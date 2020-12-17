---
title: Een tensor flow-model trainen en implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u met Azure Machine Learning een tensor flow-trainings taak kunt schalen met behulp van elastische Cloud Compute-resources.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c82b1ffbb005542822016a55346d9067e23050b2
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630867"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Tensor flow-modellen op schaal trainen met Azure Machine Learning

In dit artikel leert u hoe u uw [tensor flow](https://www.tensorflow.org/overview) -trainings scripts op schaal kunt uitvoeren met behulp van Azure machine learning.

In dit voor beeld wordt een tensor flow-model getraind en geregistreerd om handgeschreven cijfers te classificeren met behulp van een diepe Neural Network (DNN).

Of u nu een tensor flow-model ontwikkelt of een [bestaand model](how-to-deploy-existing-model.md) in de Cloud brengt, u kunt Azure machine learning gebruiken voor het uitschalen van open-source trainings taken om productie-kwaliteits modellen te bouwen, te implementeren, te maken en te bewaken.

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van de volgende omgevingen:

 - Azure Machine Learning Compute-instantie-geen down loads of installatie vereist

     - Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.
    - Zoek in de map met uitgebreide trainingen op de notebook server een volledig en uitgebreid notitie blok door naar deze map te navigeren: **instructies-to-use-azureml > ml-frameworks > tensor flow > Train-afstemming-Tune-Deploy-with-tensor flow** . 
 
 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure machine learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
    - [De voorbeeld script bestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` maar `utils.py`
     
    U kunt ook een voltooide [Jupyter notebook versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) van deze hand leiding vinden op de pagina met voor beelden van github. Het notitie blok bevat uitgebreide secties die betrekking hebben op intelligent afstemming tuning, model implementatie en notebook widgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainings experiment opgesteld door het laden van de vereiste Python-pakketten, het initialiseren van een werk ruimte, het maken van het reken doel en het definiëren van de trainings omgeving.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde python-bibliotheken.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) object te maken.

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

Gebruik de- `register()` methode om de gegevensset te registreren in uw werk ruimte, zodat deze kan worden gedeeld met anderen, opnieuw worden gebruikt in verschillende experimenten en waarnaar wordt verwezen met de naam in uw trainings script.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Een rekendoel maken

Maak een compute-doel voor uw tensor flow-taak om uit te voeren. In dit voor beeld maakt u een Azure Machine Learning Compute-cluster waarvoor GPU is ingeschakeld.

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

Als u de Azure ML- [omgeving](concept-environments.md) wilt definiëren waarin de afhankelijkheden van uw trainings script zijn opgenomen, kunt u een aangepaste omgeving definiëren of een Azure ml-gewerkte omgeving gebruiken.

#### <a name="use-a-curated-environment"></a>Een gecuratore omgeving gebruiken
Azure ML biedt vooraf ontwikkelde, geconstrueerde omgevingen als u uw eigen omgeving niet wilt definiëren. Azure ML heeft verschillende CPU-en GPU-gecuratore omgevingen voor tensor flow die overeenkomen met de verschillende versies van tensor flow. Zie [hier](resource-curated-environments.md)voor meer informatie.

Als u een gewerkte omgeving wilt gebruiken, kunt u in plaats daarvan de volgende opdracht uitvoeren:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Als u de pakketten wilt zien die zijn opgenomen in de gewerkte omgeving, kunt u de Conda-afhankelijkheden naar de schijf schrijven:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Zorg ervoor dat de gecuratorde omgeving alle afhankelijkheden bevat die vereist zijn voor uw trainings script. Als dat niet het geval is, moet u de omgeving aanpassen zodat de ontbrekende afhankelijkheden worden toegevoegd. Als de omgeving is gewijzigd, moet u deze een nieuwe naam geven, omdat het voor voegsel ' AzureML ' is gereserveerd voor de gevoerde omgevingen. Als u het YAML-bestand met Conda-afhankelijkheden hebt gewijzigd, kunt u een nieuwe omgeving maken op basis van een nieuwe naam, bijvoorbeeld:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Als u in plaats daarvan het object met de gemodificeerde omgeving rechtstreeks hebt gewijzigd, kunt u die omgeving klonen met een nieuwe naam:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Een aangepaste omgeving maken

U kunt ook uw eigen Azure ML-omgeving maken waarin de afhankelijkheden van uw trainings script worden ingekapseld.

Definieer eerst uw Conda-afhankelijkheden in een YAML-bestand. in dit voor beeld heeft het bestand de naam `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Een Azure ML-omgeving maken op basis van deze Conda Environment-specificatie. De omgeving wordt tijdens runtime ingepakt in een docker-container.

Als er geen basis installatie kopie is opgegeven, wordt door Azure ML standaard een CPU-installatie kopie gebruikt `azureml.core.environment.DEFAULT_CPU_IMAGE` als basis installatie kopie. Omdat in dit voor beeld training wordt uitgevoerd op een GPU-cluster, moet u een GPU-basis installatie kopie opgeven met de benodigde GPU-Stuur Programma's en-afhankelijkheden. Azure ML onderhoudt een set basis installatie kopieën die zijn gepubliceerd op micro soft Container Registry (MCR) die u kunt gebruiken. Zie de [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) github opslag plaats voor meer informatie.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> U kunt desgewenst al uw afhankelijkheden rechtstreeks vastleggen in een aangepaste docker-installatie kopie of Dockerfile, en uw omgeving maken op basis hiervan. Zie voor meer informatie [trainen met aangepaste installatie kopie](how-to-train-with-custom-image.md).

Zie [software omgevingen maken en gebruiken in azure machine learning](how-to-use-environments.md)voor meer informatie over het maken van en het gebruik van omgevingen.

## <a name="configure-and-submit-your-training-run"></a>Uw trainings uitvoering configureren en verzenden

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken

Maak een [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren. Eventuele argumenten voor uw trainings script worden door gegeven via de opdracht regel indien opgegeven in de `arguments` para meter.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning trainings scripts worden uitgevoerd door de hele bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [. ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neemt u het niet op in de bron directory. In plaats daarvan kunt u toegang krijgen tot uw gegevens met behulp van een Azure ML- [gegevensset](how-to-train-with-datasets.md).

Zie [trainings uitvoeringen configureren en verzenden](how-to-set-up-training-targets.md)voor meer informatie over het configureren van taken met ScriptRunConfig.

> [!WARNING]
> Als u eerder de tensor flow Estimator hebt gebruikt voor het configureren van uw tensor flow-trainings taken, moet u er rekening mee houden dat de schattingen zijn afgeschaft vanaf de 1.19.0 SDK-release. Met Azure ML SDK >= 1.15.0 is ScriptRunConfig de aanbevolen manier om trainings taken te configureren, inclusief de functies die gebruikmaken van diepe lessen. Zie voor algemene vragen over migratie de [migratie handleiding voor Estimator naar ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-a-run"></a>Een run verzenden

Het [object run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) biedt de interface voor de uitvoerings geschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = Experiment(workspace=ws, name='tf-mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Wat er gebeurt tijdens de uitvoering van het programma
Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen door lopen:

- **Voorbereiden**: een docker-installatie kopie wordt gemaakt volgens de gedefinieerde omgeving. De afbeelding wordt geüpload naar het container register van de werk ruimte en opgeslagen in de cache voor latere uitvoeringen. Logboeken worden ook gestreamd naar de uitvoerings geschiedenis en kunnen worden weer gegeven om de voortgang te bewaken. Als er in plaats daarvan een gecuratorde omgeving wordt opgegeven, wordt er een back-up van de in de cache opgeslagen installatie kopie gebruikt.

- **Schalen**: het cluster probeert omhoog te schalen als het batch AI-cluster meer knoop punten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren**: alle scripts in de map script worden geüpload naar het Compute-doel, gegevens archieven worden gekoppeld of gekopieerd en de `script` wordt uitgevoerd. Uitvoer van stdout en de map **./logs** worden gestreamd naar de uitvoerings geschiedenis en kunnen worden gebruikt om de uitvoering te bewaken.

- **Na de verwerking**: de map **./outputs** van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis.

## <a name="register-or-download-a-model"></a>Een model registreren of downloaden

Zodra u het model hebt getraind, kunt u het registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen. Optioneel: door de para meters op te geven, en wordt de `model_framework` `model_framework_version` implementatie van `resource_configuration` geen code model beschikbaar. Zo kunt u uw model rechtstreeks implementeren als een webservice van het geregistreerde model en het `ResourceConfiguration` object definieert de reken resource voor de webservice.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

U kunt ook een lokale kopie van het model downloaden met behulp van het object run. In het trainings script `tf_mnist.py` wordt het model door een tensor flow-beveiligings object naar een lokale map (lokaal naar het Compute-doel) bewaard. U kunt het object run gebruiken om een kopie te downloaden.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Gedistribueerde training

Azure Machine Learning biedt ook ondersteuning voor gedistribueerde tensor flow-taken met meerdere knoop punten zodat u de werk belasting van uw training kunt schalen. U kunt eenvoudig gedistribueerde tensor flow-taken uitvoeren en Azure ML beheert de indeling voor u.

Azure ML biedt ondersteuning voor het uitvoeren van gedistribueerde tensor flow-taken met de ingebouwde API van Horovod en tensor flow.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) is een open source-, alle verlaagde structuur voor gedistribueerde training ontwikkeld door uber. Het biedt een eenvoudig pad voor het schrijven van gedistribueerde tensor flow code voor training.

Uw trainings code moet worden beinstrumented met Horovod voor gedistribueerde trainingen. Raadpleeg Horovod-documentatie voor meer informatie over het gebruik van Horovod met tensor flow:

Raadpleeg Horovod-documentatie voor meer informatie over het gebruik van Horovod met tensor flow:

* [Horovod met tensor flow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod met de Keras-API van tensor flow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Bovendien moet u ervoor zorgen dat uw trainings omgeving het **horovod** -pakket bevat. Als u een tensor flow-gecuratorde omgeving gebruikt, is horovod al opgenomen als een van de afhankelijkheden. Als u uw eigen omgeving gebruikt, zorg er dan voor dat de horovod-afhankelijkheid is opgenomen, bijvoorbeeld:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Als u een gedistribueerde taak wilt uitvoeren met behulp van MPI/Horovod op Azure ML, moet u een [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) opgeven voor de `distributed_job_config` para meter van de ScriptRunConfig-constructor. De onderstaande code configureert een gedistribueerde taak van 2 knoop punten die per knoop punt wordt uitgevoerd. Als u ook meerdere processen per knoop punt wilt uitvoeren (dat wil zeggen, als uw cluster-SKU meerdere Gpu's heeft), geeft u ook de `process_count_per_node` para meter op in MpiConfiguration (de standaard instelling is `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Zie [gedistribueerde tensor flow met Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod)voor een volledige zelf studie over het uitvoeren van gedistribueerde tensor flow met Horovod in azure ml.

### <a name="tfdistribute"></a>tf. distribute

Als u [systeem eigen gedistribueerde tensor flow](https://www.tensorflow.org/guide/distributed_training) in uw trainings code gebruikt, bijvoorbeeld tensor Flow 2. x `tf.distribute.Strategy` API, kunt u de gedistribueerde taak ook starten via Azure ml. 

Als u dit wilt doen, geeft u een [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) op voor de `distributed_job_config` para meter van de ScriptRunConfig-constructor. Als u gebruikt `tf.distribute.experimental.MultiWorkerMirroredStrategy` , geeft u de `worker_count` in het TensorflowConfiguration op dat overeenkomt met het aantal knoop punten voor uw trainings taak.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

In tensor flow is de `TF_CONFIG` omgevings variabele vereist voor de training op meerdere computers. Azure ML configureert en stelt de `TF_CONFIG` variabele voor elke werk nemer op de juiste wijze in voordat het trainings script wordt uitgevoerd. U kunt toegang krijgen `TF_CONFIG` vanuit uw trainings script als u dit nodig hebt `os.environ['TF_CONFIG']` .

Voor beeld van structuur van `TF_CONFIG` set op een hoofd worker-knoop punt:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Als uw trainings script gebruikmaakt van de parameter Server strategie voor gedistribueerde trainingen, d.w.z. voor verouderde tensor flow 1. x, moet u ook het aantal parameter servers opgeven dat in de taak moet worden gebruikt, bijvoorbeeld `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Een tensor flow-model implementeren

De implementatie-instructie bevat een sectie over het registreren van modellen, maar u kunt direct door gaan naar het [maken van een reken doel](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

### <a name="preview-no-code-model-deployment"></a>Evaluatie Implementatie van geen code model

In plaats van de traditionele implementatie route kunt u ook de functie voor het implementeren van geen code (preview) gebruiken voor tensor flow. Als u het model registreert zoals hierboven wordt weer gegeven, `model_framework` `model_framework_version` `resource_configuration` kunt u gewoon de `deploy()` statische functie gebruiken om uw model te implementeren.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

De volledige [instructies voor](how-to-deploy-and-where.md) het implementeren van een implementatie in azure machine learning meer dieper.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een tensor flow-model getraind en geregistreerd en hebt u geleerd over opties voor implementatie. Raadpleeg de volgende artikelen voor meer informatie over Azure Machine Learning.

* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Referentie architectuur voor gedistribueerde training van diep gaande lessen in azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
