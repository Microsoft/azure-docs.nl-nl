---
title: Een TensorFlow-model trainen en implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over Azure Machine Learning u in staat stelt om een TensorFlow-trainingswerkstroom uit te schalen met behulp van rekenbronnen in de elastische cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: how-to
ms.openlocfilehash: 9875902e0197d57dfe97d24b6cfd18484a32c65f
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888400"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>TensorFlow-modellen op schaal trainen met Azure Machine Learning

In dit artikel leert u hoe u uw [TensorFlow-trainingsscripts op](https://www.tensorflow.org/overview) schaal kunt uitvoeren met behulp Azure Machine Learning.

In dit voorbeeld wordt een TensorFlow-model traint en geregistreerd om handgeschreven cijfers te classificeren met behulp van een Deep Neural Network (DNN).

Of u nu een TensorFlow-model vanaf de basis ontwikkelt of een bestaand [model naar](how-to-deploy-existing-model.md) de cloud brengt, u kunt Azure Machine Learning gebruiken om opensource-trainingstaken uit te schalen voor het bouwen, implementeren, versie- en bewaken van modellen op productiekwaliteit.

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van deze omgevingen:

 - Azure Machine Learning rekenproces : er zijn geen downloads of installatie nodig

     - Voltooi de [Zelfstudie: Omgeving en werkruimte instellen om](tutorial-1st-experiment-sdk-setup.md) een toegewezen notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de map samples deep learning op de notebookserver een voltooide en uitgebreide notebook door te navigeren naar deze map: **how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow** folder. 
 
 - Uw eigen Jupyter Notebook server

    - [Installeer de Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Maak een configuratiebestand voor de werkruimte.](how-to-configure-environment.md#workspace)
    - [De voorbeeldscriptbestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` En `utils.py`
     
    U kunt ook een voltooide Jupyter Notebook [van](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deze handleiding vinden op de pagina met GitHub-voorbeelden. Het notebook bevat uitgebreide secties over intelligente hyperparameterafstemming, modelimplementatie en notebookwidgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie stelt u het trainingsexperiment in door de vereiste Python-pakketten te laden, een werkruimte te initialiseren, het rekendoel te maken en de trainingsomgeving te definiëren.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde Python-bibliotheken.

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

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning is](concept-workspace.md) de resource op het hoogste niveau voor de service. Het biedt u een centrale plaats om te werken met alle artefacten die u maakt. In de Python-SDK hebt u toegang tot de werkruimteartefacten door een -object te [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) maken.

Maak een werkruimteobject op basis van `config.json` het bestand dat is gemaakt in de sectie met [vereisten.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Een bestandsset maken

Een `FileDataset` object verwijst naar een of meer bestanden in de gegevensstore of openbare URL's van uw werkruimte. De bestanden kunnen elke indeling hebben en de klasse biedt u de mogelijkheid om de bestanden te downloaden of aan uw rekenkracht te toevoegen. Door een te `FileDataset` maken, maakt u een verwijzing naar de locatie van de gegevensbron. Als u transformaties op de gegevensset hebt toegepast, worden deze ook opgeslagen in de gegevensset. De gegevens blijven bewaard op de bestaande locatie, dus maakt u geen extra opslagkosten. Zie de [handleiding voor](./how-to-create-register-datasets.md) het pakket voor `Dataset` meer informatie.

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

Gebruik de methode om de gegevensset te registreren bij uw werkruimte, zodat ze kunnen worden gedeeld met anderen, kunnen worden hergebruikt in verschillende experimenten en kunnen worden aangeduid met de naam in uw `register()` trainingsscript.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Een rekendoel maken

Maak een rekendoel voor uw TensorFlow-taak om op uit te voeren. In dit voorbeeld maakt u een GPU-Azure Machine Learning rekencluster.

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

Als u de Azure [ML-omgeving](concept-environments.md) wilt definiëren waarin de afhankelijkheden van uw trainingsscript zijn ingekapseld, kunt u een aangepaste omgeving definiëren of een door Azure ML gecureerde omgeving gebruiken.

#### <a name="use-a-curated-environment"></a>Een gecureerde omgeving gebruiken
Azure ML biedt vooraf samengestelde, gecureerde omgevingen als u uw eigen omgeving niet wilt definiëren. Azure ML heeft verschillende cpu- en GPU-gecureerde omgevingen voor TensorFlow die overeenkomen met verschillende versies van TensorFlow. Zie hier voor [meer informatie.](resource-curated-environments.md)

Als u een gecureerde omgeving wilt gebruiken, kunt u in plaats daarvan de volgende opdracht uitvoeren:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Als u de pakketten wilt zien die zijn opgenomen in de gecureerde omgeving, kunt u de Conda-afhankelijkheden naar de schijf schrijven:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Zorg ervoor dat de gecureerde omgeving alle afhankelijkheden bevat die vereist zijn voor uw trainingsscript. Zo niet, dan moet u de omgeving aanpassen om de ontbrekende afhankelijkheden op te nemen. Als de omgeving wordt gewijzigd, moet u deze een nieuwe naam geven, omdat het voorvoegsel 'AzureML' is gereserveerd voor gecureerde omgevingen. Als u het YAML-bestand met conda-afhankelijkheden hebt gewijzigd, kunt u er een nieuwe omgeving van maken met een nieuwe naam, bijvoorbeeld:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Als u in plaats daarvan het gecureerde omgevingsobject rechtstreeks hebt gewijzigd, kunt u die omgeving klonen met een nieuwe naam:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Een aangepaste omgeving maken

U kunt ook uw eigen Azure ML-omgeving maken die de afhankelijkheden van uw trainingsscript inkapseld.

Definieer eerst uw Conda-afhankelijkheden in een YAML-bestand; In dit voorbeeld heeft het bestand de naam `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Maak een Azure ML-omgeving aan de hand van deze Conda-omgevingsspecificatie. De omgeving wordt tijdens runtime verpakt in een Docker-container.

Als er geen basisafbeelding is opgegeven, gebruikt Azure ML standaard een CPU-afbeelding `azureml.core.environment.DEFAULT_CPU_IMAGE` als basisafbeelding. Omdat in dit voorbeeld training wordt uitgevoerd op een GPU-cluster, moet u een GPU-basisafbeelding opgeven met de benodigde GPU-stuurprogramma's en -afhankelijkheden. Azure ML onderhoudt een set basisafbeeldingen die zijn gepubliceerd op Microsoft Container Registry (MCR) die u kunt gebruiken. Zie de [GitHub-opslagplaats Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) voor meer informatie.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> U kunt eventueel al uw afhankelijkheden rechtstreeks vastleggen in een aangepaste Docker-afbeelding of Dockerfile en daar uw omgeving van maken. Zie Trainen met aangepaste afbeelding [voor meer informatie.](how-to-train-with-custom-image.md)

Zie Softwareomgevingen maken en gebruiken in Azure Machine Learning voor meer informatie over het maken [en gebruiken van omgevingen.](how-to-use-environments.md)

## <a name="configure-and-submit-your-training-run"></a>De trainingsrun configureren en verzenden

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken

Maak een [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren. Argumenten voor uw trainingsscript worden doorgegeven via de opdrachtregel als deze zijn opgegeven in de `arguments` parameter .

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
> Azure Machine Learning voert trainingsscripts uit door de volledige bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [.ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neem u deze niet op in de bronmap . Open in plaats daarvan uw gegevens met behulp van een Azure [ML-gegevensset](how-to-train-with-datasets.md).

Zie Configure [and submit training runs](how-to-set-up-training-targets.md)(Trainingsuit runs configureren en verzenden) voor meer informatie over het configureren van taken met ScriptRunConfig.

> [!WARNING]
> Als u eerder de TensorFlow-estimator hebt gebruikt om uw TensorFlow-trainingstaken te configureren, moet u er rekening mee houden dat estimators zijn afgeschaft vanaf de 1.19.0 SDK-release. Met Azure ML SDK >= 1.15.0 is ScriptRunConfig de aanbevolen manier om trainingstaken te configureren, inclusief de taken die gebruikmaken van frameworks voor deep learning. Zie de migratiehandleiding [Estimator to ScriptRunConfig voor](how-to-migrate-from-estimators-to-scriptrunconfig.md)veelvoorkomende migratievragen.

### <a name="submit-a-run"></a>Een run verzenden

Het [object Run](/python/api/azureml-core/azureml.core.run%28class%29) biedt de interface naar de run history terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Wat er gebeurt tijdens de uitvoering
Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen uitgevoerd:

- **Voorbereiden:** er wordt een Docker-afbeelding gemaakt op basis van de omgeving die is gedefinieerd. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere runs. Logboeken worden ook gestreamd naar de uitvoeringsgeschiedenis en kunnen worden bekeken om de voortgang te controleren. Als in plaats daarvan een gecureerde omgeving wordt opgegeven, wordt de back-of-backing van de gecureerde omgeving in de cache gebruikt.

- **Schalen:** het cluster probeert omhoog te schalen als het Batch AI cluster meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren:** alle scripts in de scriptmap worden geüpload naar het rekendoel, gegevensopslag worden aan of gekopieerd en de `script` wordt uitgevoerd. Uitvoer van stdout en de **map ./logs** worden gestreamd naar de uitvoergeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Naverwerking:** de **map ./outputs** van de run wordt gekopieerd naar de uitvoergeschiedenis.

## <a name="register-or-download-a-model"></a>Een model registreren of downloaden

Zodra u het model hebt getraind, kunt u het registreren bij uw werkruimte. Met modelregistratie kunt u uw modellen opslaan en versiebeheeren in uw werkruimte om [het modelbeheer en de implementatie te vereenvoudigen.](concept-model-management-and-deployment.md) Optioneel: door de parameters , en op te geven, wordt de implementatie van `model_framework` het model zonder code `model_framework_version` `resource_configuration` beschikbaar. Hierdoor kunt u uw model rechtstreeks implementeren als een webservice vanuit het geregistreerde model en definieert het -object de `ResourceConfiguration` rekenresource voor de webservice.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

U kunt ook een lokale kopie van het model downloaden met behulp van het object Run. In het trainingsscript wordt het model met een TensorFlow-opslagobject opgeslagen in een lokale `tf_mnist.py` map (lokaal voor het rekendoel). U kunt het run-object gebruiken om een kopie te downloaden.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Gedistribueerde training

Azure Machine Learning ondersteunt ook gedistribueerde TensorFlow-taken met meerdere knooppunt, zodat u uw trainingsworkloads kunt schalen. U kunt eenvoudig gedistribueerde TensorFlow-taken uitvoeren en Azure ML beheert de orchestration voor u.

Azure ML ondersteunt het uitvoeren van gedistribueerde TensorFlow-taken met zowel Horovod als de ingebouwde api voor gedistribueerde training van TensorFlow.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) is een open source, all reduce framework voor gedistribueerde training dat is ontwikkeld door Uber. Het biedt een eenvoudig pad voor het schrijven van gedistribueerde TensorFlow-code voor training.

Uw trainingscode moet worden instrumenteerd met Horovod voor gedistribueerde training. Raadpleeg de Horovod-documentatie voor meer informatie over het gebruik van Horovod met TensorFlow:

Raadpleeg de Horovod-documentatie voor meer informatie over het gebruik van Horovod met TensorFlow:

* [Horovod met TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod met Keras-API van TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Zorg er ook voor dat uw trainingsomgeving het **horovod-pakket** bevat. Als u een door TensorFlow gecureerde omgeving gebruikt, is horovod al opgenomen als een van de afhankelijkheden. Als u uw eigen omgeving gebruikt, moet u ervoor zorgen dat de horovod-afhankelijkheid is opgenomen, bijvoorbeeld:

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

Als u een gedistribueerde taak wilt uitvoeren met behulp van MPI/Horovod in Azure ML, moet u een [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) opgeven voor de parameter van de `distributed_job_config` ScriptRunConfig-constructor. Met de onderstaande code wordt een gedistribueerde taak met twee knooppunt geconfigureerd met één proces per knooppunt. Als u ook meerdere processen per knooppunt wilt uitvoeren (dat wil zeggen als uw cluster-SKU meerdere GPU's heeft), geeft u daarnaast de `process_count_per_node` parameter op in MpiConfiguration (de standaardwaarde is `1` ).

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

Zie Distributed [TensorFlow with Horovod (Gedistribueerde TensorFlow met Horovod)](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod)voor een volledige zelfstudie over het uitvoeren van gedistribueerde TensorFlow met Horovod op Azure ML.

### <a name="tfdistribute"></a>tf.distribute

Als u native gedistribueerde [TensorFlow](https://www.tensorflow.org/guide/distributed_training) gebruikt in uw trainingscode, bijvoorbeeld de API van TensorFlow 2.x, kunt u de gedistribueerde taak ook starten `tf.distribute.Strategy` via Azure ML. 

Geef een [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) op als parameter van de `distributed_job_config` ScriptRunConfig-constructor. Als u `tf.distribute.experimental.MultiWorkerMirroredStrategy` gebruikt, geeft u `worker_count` de op in de TensorflowConfiguration die overeenkomt met het aantal knooppunten voor uw trainings job.

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

In TensorFlow is de `TF_CONFIG` omgevingsvariabele vereist voor training op meerdere computers. Azure ML configureert en stelt de variabele `TF_CONFIG` op de juiste wijze in voor elke werker voordat u het trainingsscript gaat uitvoeren. U kunt toegang `TF_CONFIG` krijgen vanuit uw trainingsscript als dat nodig is via `os.environ['TF_CONFIG']` .

Voorbeeldstructuur van `TF_CONFIG` set op een Chief Worker-knooppunt:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Als uw trainingsscript gebruikmaakt van de parameterserverstrategie voor gedistribueerde training, dat wil zeggen voor verouderde TensorFlow 1.x, moet u ook het aantal parameterservers opgeven dat in de taak moet worden gebruikt, bijvoorbeeld `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Een TensorFlow-model implementeren

De implementatie-how-to bevat een sectie over het registreren van modellen, maar u kunt direct naar het maken van een [rekendoel](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie gaan, omdat u al een geregistreerd model hebt.

### <a name="preview-no-code-model-deployment"></a>(Preview) Implementatie van model zonder code

In plaats van de traditionele implementatieroute kunt u ook de implementatiefunctie zonder code (preview) voor TensorFlow gebruiken. Door uw model te registreren zoals hierboven wordt weergegeven met de parameters , en , kunt u gewoon de statische functie `model_framework` gebruiken om uw model te `model_framework_version` `resource_configuration` `deploy()` implementeren.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

De volledige [informatie over implementatie](how-to-deploy-and-where.md) in Azure Machine Learning uitgebreider.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een TensorFlow-model getraind en geregistreerd en meer geleerd over de opties voor implementatie. Zie deze andere artikelen voor meer informatie over Azure Machine Learning.

* [Metrische gegevens van de run bijhouden tijdens de training](how-to-log-view-metrics.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Referentiearchitectuur voor gedistribueerde deep learning-training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
