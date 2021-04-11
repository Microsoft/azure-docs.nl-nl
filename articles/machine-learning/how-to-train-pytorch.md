---
title: PyTorch-modellen voor diepe Learning trainen
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van uw PyTorch-trainings scripts op ENTER prise Scale met behulp van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b1cb14e07f6c0e402510abad6f1cb160f5215c63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518378"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>PyTorch-modellen op schaal trainen met Azure Machine Learning

In dit artikel leert u hoe u uw [PyTorch](https://pytorch.org/) -trainings scripts kunt uitvoeren op ENTER prise Scale met behulp van Azure machine learning.

De voorbeeld scripts in dit artikel worden gebruikt voor het classificeren van kippen en Turkije-installatie kopieën voor het bouwen van een diep gaande Neural-netwerk (DNN) op basis van de [zelf studie](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)over de overdrachts training van PyTorch. Overboeking learning is een techniek waarbij kennis wordt toegepast op basis van het oplossen van een probleem naar een ander, maar verwant probleem. Hiermee wordt het trainings proces versneld door minder gegevens-, tijd-en reken bronnen te vereisen dan bij de nieuwe training. Raadpleeg het artikel over [uitgebreide kennis en machine learning](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) voor meer informatie over overboeking learning.

Of u nu een diep Learning PyTorch-model traint of een bestaand model in de Cloud brengt, u kunt Azure Machine Learning gebruiken om open-source trainings taken te schalen met behulp van elastische Cloud Compute-resources. U kunt modellen voor productie kwaliteit bouwen, implementeren, versie en bewaken met Azure Machine Learning. 

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van de volgende omgevingen:

- Azure Machine Learning Compute-instantie-geen down loads of installatie vereist

    - Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.
    - Zoek in de map met uitgebreide trainingen op de notebook server een volledig en uitgebreid notitie blok door naar deze map te navigeren: **instructies-to-use-azureml > ml-frameworks > pytorch > Train-afstemming-Tune-Deploy-with-pytorch** . 
 
 - Uw eigen Jupyter Notebook-server
    - [Installeer de Azure machine learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
    - [De voorbeeld script bestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    U kunt ook een voltooide [Jupyter notebook versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) van deze hand leiding vinden op de pagina met voor beelden van github. Het notitie blok bevat uitgebreide secties die betrekking hebben op intelligent afstemming tuning, model implementatie en notebook widgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainings experiment opgesteld door het laden van de vereiste Python-pakketten, het initialiseren van een werk ruimte, het maken van het reken doel en het definiëren van de trainings omgeving.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde python-bibliotheken.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) object te maken.

Maak een werkruimte object op basis van het `config.json` bestand dat in de [sectie vereisten](#prerequisites)is gemaakt.

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>De gegevens ophalen

De gegevensset bestaat uit ongeveer 120 trainings afbeeldingen voor kalk oenen en kuikens, met 100-validatie kopieën voor elke klasse. We zullen de gegevensset downloaden en uitpakken als onderdeel van het trainings script `pytorch_train.py` . De installatie kopieën zijn een subset van de [Open Image V5-gegevensset](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Trainings script voorbereiden

In deze zelf studie wordt het trainings script, `pytorch_train.py` ,, al meegeleverd. In de praktijk kunt u een aangepast trainings script uitvoeren, zoals dat is, en dit met Azure Machine Learning.

Maak een map voor uw trainings script (s).

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Een rekendoel maken

Maak een compute-doel voor uw PyTorch-taak om uit te voeren. In dit voor beeld maakt u een Azure Machine Learning Compute-cluster waarvoor GPU is ingeschakeld.

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
Azure ML biedt vooraf ontwikkelde, geconstrueerde omgevingen als u uw eigen omgeving niet wilt definiëren. Azure ML heeft verschillende CPU-en GPU-gecuratore omgevingen voor PyTorch die overeenkomen met de verschillende versies van PyTorch. Zie [hier](resource-curated-environments.md)voor meer informatie.

Als u een gewerkte omgeving wilt gebruiken, kunt u in plaats daarvan de volgende opdracht uitvoeren:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Als u de pakketten wilt zien die zijn opgenomen in de gewerkte omgeving, kunt u de Conda-afhankelijkheden naar de schijf schrijven:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Zorg ervoor dat de gecuratorde omgeving alle afhankelijkheden bevat die vereist zijn voor uw trainings script. Als dat niet het geval is, moet u de omgeving aanpassen zodat de ontbrekende afhankelijkheden worden toegevoegd. Als de omgeving is gewijzigd, moet u deze een nieuwe naam geven, omdat het voor voegsel ' AzureML ' is gereserveerd voor de gevoerde omgevingen. Als u het YAML-bestand met Conda-afhankelijkheden hebt gewijzigd, kunt u een nieuwe omgeving maken op basis van een nieuwe naam, bijvoorbeeld:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Als u in plaats daarvan het object met de gemodificeerde omgeving rechtstreeks hebt gewijzigd, kunt u die omgeving klonen met een nieuwe naam:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Een Azure ML-omgeving maken op basis van deze Conda Environment-specificatie. De omgeving wordt tijdens runtime ingepakt in een docker-container.

Als er geen basis installatie kopie is opgegeven, wordt door Azure ML standaard een CPU-installatie kopie gebruikt `azureml.core.environment.DEFAULT_CPU_IMAGE` als basis installatie kopie. Omdat in dit voor beeld training wordt uitgevoerd op een GPU-cluster, moet u een GPU-basis installatie kopie opgeven met de benodigde GPU-Stuur Programma's en-afhankelijkheden. Azure ML onderhoudt een set basis installatie kopieën die zijn gepubliceerd op micro soft Container Registry (MCR) die u kunt gebruiken. Zie de [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) github opslag plaats voor meer informatie.

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> U kunt desgewenst al uw afhankelijkheden rechtstreeks vastleggen in een aangepaste docker-installatie kopie of Dockerfile, en uw omgeving maken op basis hiervan. Zie voor meer informatie [trainen met aangepaste installatie kopie](how-to-train-with-custom-image.md).

Zie [software omgevingen maken en gebruiken in azure machine learning](how-to-use-environments.md)voor meer informatie over het maken van en het gebruik van omgevingen.

## <a name="configure-and-submit-your-training-run"></a>Uw trainings uitvoering configureren en verzenden

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken

Maak een [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren. Eventuele argumenten voor uw trainings script worden door gegeven via de opdracht regel indien opgegeven in de `arguments` para meter. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning trainings scripts worden uitgevoerd door de hele bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [. ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neemt u het niet op in de bron directory. In plaats daarvan kunt u toegang krijgen tot uw gegevens met behulp van een Azure ML- [gegevensset](how-to-train-with-datasets.md).

Zie [trainings uitvoeringen configureren en verzenden](how-to-set-up-training-targets.md)voor meer informatie over het configureren van taken met ScriptRunConfig.

> [!WARNING]
> Als u eerder de PyTorch Estimator hebt gebruikt voor het configureren van uw PyTorch-trainings taken, moet u er rekening mee houden dat de schattingen zijn afgeschaft vanaf de 1.19.0 SDK-release. Met Azure ML SDK >= 1.15.0 is ScriptRunConfig de aanbevolen manier om trainings taken te configureren, inclusief de functies die gebruikmaken van diepe lessen. Zie voor algemene vragen over migratie de [migratie handleiding voor Estimator naar ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>Uw uitvoering verzenden

Het [object run](/python/api/azureml-core/azureml.core.run%28class%29) biedt de interface voor de uitvoerings geschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Wat er gebeurt tijdens de uitvoering van het programma
Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen door lopen:

- **Voorbereiden**: een docker-installatie kopie wordt gemaakt volgens de gedefinieerde omgeving. De afbeelding wordt geüpload naar het container register van de werk ruimte en opgeslagen in de cache voor latere uitvoeringen. Logboeken worden ook gestreamd naar de uitvoerings geschiedenis en kunnen worden weer gegeven om de voortgang te bewaken. Als er in plaats daarvan een gecuratorde omgeving wordt opgegeven, wordt er een back-up van de in de cache opgeslagen installatie kopie gebruikt.

- **Schalen**: het cluster probeert omhoog te schalen als het batch AI-cluster meer knoop punten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren**: alle scripts in de map script worden geüpload naar het Compute-doel, gegevens archieven worden gekoppeld of gekopieerd en de `script` wordt uitgevoerd. Uitvoer van stdout en de map **./logs** worden gestreamd naar de uitvoerings geschiedenis en kunnen worden gebruikt om de uitvoering te bewaken.

- **Na de verwerking**: de map **./outputs** van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis.

## <a name="register-or-download-a-model"></a>Een model registreren of downloaden

Zodra u het model hebt getraind, kunt u het registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> De implementatie-instructie bevat een sectie over het registreren van modellen, maar u kunt direct door gaan naar het [maken van een reken doel](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

U kunt ook een lokale kopie van het model downloaden met behulp van het object run. In het trainings script `pytorch_train.py` slaat een PyTorch-object opslaan het model op in een lokale map (lokaal naar het Compute-doel). U kunt het object run gebruiken om een kopie te downloaden.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Gedistribueerde training

Azure Machine Learning biedt ook ondersteuning voor gedistribueerde PyTorch-taken met meerdere knoop punten zodat u de werk belasting van uw training kunt schalen. U kunt eenvoudig gedistribueerde PyTorch-taken uitvoeren en Azure ML beheert de indeling voor u.

Azure ML biedt ondersteuning voor het uitvoeren van gedistribueerde PyTorch-taken met de ingebouwde DistributedDataParallel module Horovod en PyTorch.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) is een open source-, alle verlaagde structuur voor gedistribueerde training ontwikkeld door uber. Het biedt een eenvoudig pad voor het schrijven van gedistribueerde PyTorch code voor training.

Uw trainings code moet worden beinstrumented met Horovod voor gedistribueerde trainingen. Zie de [Horovod-documentatie](https://horovod.readthedocs.io/en/stable/pytorch.html)voor meer informatie over het gebruik van Horovod met PyTorch.

Bovendien moet u ervoor zorgen dat uw trainings omgeving het **horovod** -pakket bevat. Als u een PyTorch-gecuratorde omgeving gebruikt, is horovod al opgenomen als een van de afhankelijkheden. Als u uw eigen omgeving gebruikt, zorg er dan voor dat de horovod-afhankelijkheid is opgenomen, bijvoorbeeld:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Als u een gedistribueerde taak wilt uitvoeren met behulp van MPI/Horovod op Azure ML, moet u een [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) opgeven voor de `distributed_job_config` para meter van de ScriptRunConfig-constructor. De onderstaande code configureert een gedistribueerde taak van 2 knoop punten die per knoop punt wordt uitgevoerd. Als u ook meerdere processen per knoop punt wilt uitvoeren (dat wil zeggen, als uw cluster-SKU meerdere Gpu's heeft), geeft u ook de `process_count_per_node` para meter op in MpiConfiguration (de standaard instelling is `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Zie [gedistribueerde PyTorch met Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod)voor een volledige zelf studie over het uitvoeren van gedistribueerde PyTorch met Horovod in azure ml.

### <a name="distributeddataparallel"></a>DistributedDataParallel
Als u de ingebouwde [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) -module van PyTorch gebruikt die is gebouwd met behulp van het **Torch. distributed** -pakket in uw trainings code, kunt u de gedistribueerde taak ook starten via Azure ml.

Als u een gedistribueerde PyTorch-taak op Azure ML wilt starten, hebt u twee opties:
1. Starten per proces: Geef het totale aantal werk processen op dat u wilt uitvoeren en Azure ML voert elk proces uit.
2. Starten per knoop punt met `torch.distributed.launch` : Geef de `torch.distributed.launch` opdracht op die u wilt uitvoeren op elk knoop punt. Het hulp programma Torch Launch zorgt voor het starten van de werk processen op elk knoop punt.

Er zijn geen fundamentele verschillen tussen deze start opties; het is grotendeels de voor keur van de gebruiker of de conventies van de frameworks/bibliotheken die zijn gebouwd boven op vanille PyTorch (zoals bliksem of hugging gezicht).

#### <a name="per-process-launch"></a>Starten per proces
Ga als volgt te werk om deze optie te gebruiken om een gedistribueerde PyTorch-taak uit te voeren:
1. Het trainings script en de argumenten opgeven
2. Maak een [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) en geef de en op `process_count` `node_count` . De `process_count` komt overeen met het totale aantal processen dat u wilt uitvoeren voor uw taak. Dit moet doorgaans gelijk zijn aan het aantal Gpu's per knoop punt vermenigvuldigd met het aantal knoop punten. Als `process_count` niet is opgegeven, wordt door Azure ml standaard één proces per knoop punt gestart.

Met Azure ML worden de volgende omgevings variabelen ingesteld:
* `MASTER_ADDR` -IP-adres van de computer die als host fungeert voor het proces met positie 0.
* `MASTER_PORT` -Een gratis poort op de computer waarop het proces wordt gehost met de positie 0.
* `NODE_RANK` -De positie van het knoop punt voor training met meerdere knoop punten. De mogelijke waarden zijn 0 tot (totaal aantal knoop punten-1).
* `WORLD_SIZE` -Het totale aantal processen. Dit moet gelijk zijn aan het totale aantal apparaten (GPU) dat wordt gebruikt voor gedistribueerde training.
* `RANK` -De (globale) positie van het huidige proces. De mogelijke waarden zijn 0 tot (wereld wijde grootte-1).
* `LOCAL_RANK` -De lokale (relatieve) positie van het proces binnen het knoop punt. De mogelijke waarden zijn 0 tot (aantal processen op het knoop punt-1).

Aangezien de vereiste omgevings variabelen voor u door Azure ML worden ingesteld, kunt u [de standaard methode voor het initialiseren van omgevings](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) variabelen gebruiken om de proces groep in uw trainings code te initialiseren.

Met het volgende code fragment wordt een PyTorch-taak van 2 knoop punten, per knoop punt, geconfigureerd:
```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=4, node_count=2)

src = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 25],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

> [!WARNING]
> Als u deze optie wilt gebruiken voor trainingen met meerdere processen per knoop punt, moet u Azure ML python SDK >= 1.22.0 gebruiken, zoals `process_count` is geïntroduceerd in 1.22.0.

> [!TIP]
> Als uw trainings script informatie als lokale positie of rang geeft als script argumenten, kunt u verwijzen naar de omgevings variabele (n) in de argumenten: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Starten per knoop punt met `torch.distributed.launch`
PyTorch biedt een start hulpprogramma in [Torch. distributed. Launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) dat gebruikers kunnen gebruiken om meerdere processen per knoop punt te starten. `torch.distributed.launch`In de module worden meerdere trainings processen op elk van de knoop punten gestart.

De volgende stappen laten zien hoe u een PyTorch-taak configureert met een Launcher per knoop punt op Azure ML, waarmee het equivalent van het uitvoeren van de volgende opdracht wordt bereikt:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Geef de `torch.distributed.launch` opdracht op voor de `command` para meter van de `ScriptRunConfig` constructor. Azure ML voert deze opdracht uit op elk knoop punt van uw trainings cluster. `--nproc_per_node` moet kleiner zijn dan of gelijk zijn aan het aantal Gpu's dat op elk knoop punt beschikbaar is. `MASTER_ADDR`, `MASTER_PORT` en `NODE_RANK` zijn allemaal ingesteld door Azure ml, dus u kunt alleen verwijzen naar de omgevings variabelen in de opdracht. Azure ML wordt ingesteld `MASTER_PORT` op 6105, maar u kunt desgewenst een andere waarde door geven aan het `--master_port` argument van de `torch.distributed.launch` opdracht. (Met het hulp programma starten worden de omgevings variabelen opnieuw ingesteld.)
2. Maak een `PyTorchConfiguration` en geef de op `node_count` . U hoeft niet in te stellen `process_count` als Azure ml om één proces per knoop punt te starten, waarmee de door u opgegeven start opdracht wordt uitgevoerd.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 2 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

src = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

Zie [gedistribueerde PyTorch met DistributedDataParallel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel)voor een volledige zelf studie over het uitvoeren van gedistribueerde PyTorch in azure ml.

### <a name="troubleshooting"></a>Problemen oplossen

* **Horovod is uitgeschakeld**: in de meeste gevallen is er sprake van een onderliggende uitzonde ring in een van de processen waardoor Horovod werd afgesloten als u zich tegen komt "AbortedError: Horovod is afgesloten". Elke classificatie in de MPI-taak krijgt een eigen toegewezen logboekbestand in Azure ML. Deze logboeken hebben de naam `70_driver_logs`. In het geval van gedistribueerde trainingen worden de logboeknamen aangevuld met het achtervoegsel `_rank` om het onderscheiden van de logboeken gemakkelijker te maken. Als u de exacte fout wilt vinden die ervoor heeft gezorgd dat Horovod wordt afgesloten, gaat u naar alle logboek bestanden en zoekt u `Traceback` aan het einde van de driver_log bestanden. Met een van deze bestanden krijgt u de daad werkelijke onderliggende uitzonde ring. 

## <a name="export-to-onnx"></a>Exporteren naar ONNX

Converteer uw getrainde PyTorch-model naar de ONNX-indeling om de deinterferentie te optimaliseren met de [ONNX-runtime](concept-onnx.md). Defactorion of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor de voor spelling, meestal op productie gegevens. Raadpleeg de [zelf studie](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) voor een voor beeld.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een diep gaande Neural-netwerk getraind en geregistreerd met behulp van PyTorch in Azure Machine Learning. Ga verder met ons model implementatie artikel voor meer informatie over het implementeren van een model.

* [Hoe en waar modellen moeten worden geïmplementeerd](how-to-deploy-and-where.md)
* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentie architectuur voor gedistribueerde training van diep gaande lessen in azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
