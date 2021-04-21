---
title: PyTorch-modellen voor deep learning trainen
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van uw PyTorch-trainingsscripts op ondernemingsschaal met behulp van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5a955424f3fb91a38e9061966ed742922913f1c4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819156"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>PyTorch-modellen op schaal trainen met Azure Machine Learning

In dit artikel leert u hoe u uw [PyTorch-trainingsscripts](https://pytorch.org/) op ondernemingsschaal kunt uitvoeren met behulp van Azure Machine Learning.

De voorbeeldscripts in dit artikel worden gebruikt voor het classificeren van afbeeldingen voor het classificeren van [](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)afbeeldingen van het deep learning-neuraal netwerk (DNN) op basis van de zelfstudie over leeroverdracht van PyTorch. Kennisoverdracht is een techniek waarmee kennis die is opgedaan door het oplossen van één probleem, wordt toegepast op een ander, maar gerelateerd probleem. Hiermee wordt een snelkoppeling gemaakt naar het trainingsproces door minder gegevens-, tijd- en rekenbronnen te vereisen dan een nieuwe training. Zie het [artikel Deep Learning versus machine learning](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) meer informatie over leeroverdracht.

Of u nu een deep learning PyTorch-model vanaf de basis traint of een bestaand model naar de cloud brengt, u kunt Azure Machine Learning gebruiken om opensource-trainingstaken uit te schalen met behulp van rekenbronnen voor elastische cloud. U kunt productiemodellen bouwen, implementeren, versieverbouwen en bewaken met Azure Machine Learning. 

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van deze omgevingen:

- Azure Machine Learning rekenproces : er zijn geen downloads of installatie nodig

    - Voltooi de [Zelfstudie: Omgeving en werkruimte instellen om](tutorial-1st-experiment-sdk-setup.md) een toegewezen notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de map samples deep learning op de notebookserver een voltooid en uitgebreid notebook door te navigeren naar deze map: **how-to-use-azureml > ml-frameworks > pytorch > train-hyperparameter-tune-deploy-with-pytorch** folder. 
 
 - Uw eigen Jupyter Notebook server
    - [Installeer de Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Maak een configuratiebestand voor de werkruimte.](how-to-configure-environment.md#workspace)
    - [De voorbeeldscriptbestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    U kunt ook een voltooide Jupyter Notebook [van](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deze handleiding vinden op de pagina met GitHub-voorbeelden. Het notebook bevat uitgebreide secties over intelligente afstemming van hyperparameters, modelimplementatie en notebookwidgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie stelt u het trainingsexperiment in door de vereiste Python-pakketten te laden, een werkruimte te initialiseren, het rekendoel te maken en de trainingsomgeving te definiëren.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde Python-bibliotheken.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
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

### <a name="get-the-data"></a>De gegevens ophalen

De gegevensset bestaat uit ongeveer 120 trainingsafbeeldingen voor alle klassen, met 100 validatieafbeeldingen voor elke klasse. We downloaden en extraheren de gegevensset als onderdeel van ons trainingsscript `pytorch_train.py` . De afbeeldingen zijn een subset van de [Open Images v5 Dataset.](https://storage.googleapis.com/openimages/web/index.html)

### <a name="prepare-training-script"></a>Trainingsscript voorbereiden

In deze zelfstudie is het trainingsscript, `pytorch_train.py` , al opgegeven. In de praktijk kunt u elk aangepast trainingsscript gebruiken en dit uitvoeren met Azure Machine Learning.

Maak een map voor uw trainingsscript(s).

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Een rekendoel maken

Maak een rekendoel voor uw PyTorch-taak om op uit te voeren. In dit voorbeeld maakt u een GPU-Azure Machine Learning rekencluster.

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
Azure ML biedt vooraf samengestelde, gecureerde omgevingen als u uw eigen omgeving niet wilt definiëren. Azure ML heeft verschillende cpu- en GPU-gecureerde omgevingen voor PyTorch die overeenkomen met verschillende versies van PyTorch. Zie hier voor [meer informatie.](resource-curated-environments.md)

Als u een gecureerde omgeving wilt gebruiken, kunt u in plaats daarvan de volgende opdracht uitvoeren:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Als u de pakketten wilt zien die zijn opgenomen in de gecureerde omgeving, kunt u de Conda-afhankelijkheden naar de schijf schrijven:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Zorg ervoor dat de gecureerde omgeving alle afhankelijkheden bevat die vereist zijn voor uw trainingsscript. Zo niet, dan moet u de omgeving aanpassen om de ontbrekende afhankelijkheden op te nemen. Als de omgeving wordt gewijzigd, moet u deze een nieuwe naam geven, omdat het voorvoegsel 'AzureML' is gereserveerd voor gecureerde omgevingen. Als u het YAML-bestand met conda-afhankelijkheden hebt gewijzigd, kunt u er een nieuwe omgeving van maken met een nieuwe naam, bijvoorbeeld:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Als u in plaats daarvan het gecureerde omgevingsobject rechtstreeks hebt gewijzigd, kunt u die omgeving klonen met een nieuwe naam:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Maak een Azure ML-omgeving aan de hand van deze Conda-omgevingsspecificatie. De omgeving wordt tijdens runtime verpakt in een Docker-container.

Als er geen basisafbeelding is opgegeven, gebruikt Azure ML standaard een CPU-afbeelding `azureml.core.environment.DEFAULT_CPU_IMAGE` als basisafbeelding. Omdat in dit voorbeeld training wordt uitgevoerd op een GPU-cluster, moet u een GPU-basisafbeelding opgeven met de benodigde GPU-stuurprogramma's en -afhankelijkheden. Azure ML onderhoudt een set basisafbeeldingen die zijn gepubliceerd op Microsoft Container Registry (MCR) die u kunt gebruiken. Zie de [GitHub-opslagplaats Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) voor meer informatie.

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> U kunt eventueel al uw afhankelijkheden rechtstreeks vastleggen in een aangepaste Docker-afbeelding of Dockerfile en daar uw omgeving van maken. Zie Trainen met aangepaste afbeelding [voor meer informatie.](how-to-train-with-custom-image.md)

Zie Softwareomgevingen maken en gebruiken in Azure Machine Learning voor meer informatie over het maken [en gebruiken van omgevingen.](how-to-use-environments.md)

## <a name="configure-and-submit-your-training-run"></a>Uw trainingsrun configureren en verzenden

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken

Maak een [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren. Argumenten voor uw trainingsscript worden doorgegeven via de opdrachtregel als deze zijn opgegeven in de `arguments` parameter . 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning voert trainingsscripts uit door de volledige bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [.ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neem u deze niet op in de bronmap . Open in plaats daarvan uw gegevens met behulp van een Azure [ML-gegevensset](how-to-train-with-datasets.md).

Zie Configure [and submit training runs](how-to-set-up-training-targets.md)(Trainingsuit runs configureren en verzenden) voor meer informatie over het configureren van taken met ScriptRunConfig.

> [!WARNING]
> Als u eerder de PyTorch-estimator hebt gebruikt om uw PyTorch-trainingstaken te configureren, moet u er rekening mee houden dat estimators vanaf de 1.19.0 SDK-release zijn afgeschaft. Met Azure ML SDK >= 1.15.0 is ScriptRunConfig de aanbevolen manier om trainingstaken te configureren, inclusief de taken die gebruikmaken van frameworks voor deep learning. Zie de migratiehandleiding [Estimator to ScriptRunConfig voor](how-to-migrate-from-estimators-to-scriptrunconfig.md)veelvoorkomende migratievragen.

## <a name="submit-your-run"></a>Uw run verzenden

Het [object Run](/python/api/azureml-core/azureml.core.run%28class%29) biedt de interface naar de run history terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Wat er gebeurt tijdens de uitvoering
Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen uitgevoerd:

- **Voorbereiden:** er wordt een Docker-afbeelding gemaakt op basis van de omgeving die is gedefinieerd. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere runs. Logboeken worden ook gestreamd naar de uitvoeringsgeschiedenis en kunnen worden bekeken om de voortgang te controleren. Als in plaats daarvan een gecureerde omgeving wordt opgegeven, wordt de back-back-van die gecureerde omgeving in de cache gebruikt.

- **Schalen:** het cluster probeert omhoog te schalen als het Batch AI cluster meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren:** alle scripts in de scriptmap worden geüpload naar het rekendoel, gegevensopslag worden aan of gekopieerd en de `script` wordt uitgevoerd. Uitvoer van stdout en de **map ./logs** worden gestreamd naar de uitvoergeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Naverwerking:** de **map ./outputs** van de run wordt gekopieerd naar de uitvoergeschiedenis.

## <a name="register-or-download-a-model"></a>Een model registreren of downloaden

Zodra u het model hebt getraind, kunt u het registreren bij uw werkruimte. Met modelregistratie kunt u uw modellen opslaan en versiebeheeren in uw werkruimte om [het modelbeheer en de implementatie te vereenvoudigen.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> De implementatie-how-to bevat een sectie over het registreren [](how-to-deploy-and-where.md#choose-a-compute-target) van modellen, maar u kunt direct verder gaan met het maken van een rekendoel voor implementatie, omdat u al een geregistreerd model hebt.

U kunt ook een lokale kopie van het model downloaden met behulp van het object Run. In het trainingsscript wordt het model met een PyTorch-opgeslagen object opgeslagen in een lokale map `pytorch_train.py` (lokaal voor het rekendoel). U kunt het run-object gebruiken om een kopie te downloaden.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Gedistribueerde training

Azure Machine Learning ondersteunt ook gedistribueerde PyTorch-taken met meerdere knooppunt, zodat u uw trainingsworkloads kunt schalen. U kunt eenvoudig gedistribueerde PyTorch-taken uitvoeren en Azure ML beheert de orchestration voor u.

Azure ML ondersteunt het uitvoeren van gedistribueerde PyTorch-taken met zowel Horovod als de ingebouwde DistributedDataParallel-module van PyTorch.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) is een open source, all reduce framework voor gedistribueerde training dat is ontwikkeld door Uber. Het biedt een eenvoudig pad voor het schrijven van gedistribueerde PyTorch-code voor training.

Uw trainingscode moet worden instrumenteerd met Horovod voor gedistribueerde training. Zie de [Horovod-documentatie](https://horovod.readthedocs.io/en/stable/pytorch.html)voor meer informatie over het gebruik van Horovod met PyTorch.

Zorg er ook voor dat uw trainingsomgeving het **horovod-pakket** bevat. Als u een door PyTorch gecureerde omgeving gebruikt, is horovod al opgenomen als een van de afhankelijkheden. Als u uw eigen omgeving gebruikt, moet u ervoor zorgen dat de horovod-afhankelijkheid is opgenomen, bijvoorbeeld:

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

Als u een gedistribueerde taak wilt uitvoeren met behulp van MPI/Horovod in Azure ML, moet u een [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) opgeven voor de parameter van de `distributed_job_config` ScriptRunConfig-constructor. Met de onderstaande code wordt een gedistribueerde taak met twee knooppunt geconfigureerd met één proces per knooppunt. Als u ook meerdere processen per knooppunt wilt uitvoeren (dat wil zeggen als uw cluster-SKU meerdere GPU's heeft), geeft u daarnaast de `process_count_per_node` parameter op in MpiConfiguration (de standaardwaarde is `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Zie Distributed [PyTorch with Horovod (Gedistribueerde PyTorch met Horovod)](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod)voor een volledige zelfstudie over het uitvoeren van gedistribueerde PyTorch met Horovod op Azure ML.

### <a name="distributeddataparallel"></a>DistributedDataParallel
Als u de ingebouwde [DistributedDataParallel-module](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) van PyTorch gebruikt die is gebouwd met behulp van het **distributed.distributed-pakket** in uw trainingscode, kunt u de gedistribueerde taak ook starten via Azure ML.

Als u een gedistribueerde PyTorch-taak wilt starten in Azure ML, hebt u twee opties:
1. Starten per proces: geef het totale aantal werkprocessen op dat u wilt uitvoeren en Azure ML verwerkt het starten van elk proces.
2. Starten per knooppunt met `torch.distributed.launch` : geef de opdracht op die u op elk `torch.distributed.launch` knooppunt wilt uitvoeren. Het hulpprogramma voor het starten van de programma's verwerkt het starten van de werkprocessen op elk knooppunt.

Er zijn geen fundamentele verschillen tussen deze opstartopties; Het is grotendeels aan de voorkeur van de gebruiker of de conventies van de frameworks/bibliotheken die zijn gebouwd op vanille PyTorch (zoals Bliksem of Hugging Face).

#### <a name="per-process-launch"></a>Starten per proces
Ga als volgt te werk om deze optie te gebruiken om een gedistribueerde PyTorch-taak uit te voeren:
1. Het trainingsscript en de argumenten opgeven
2. Maak een [PyTorchConfiguration en](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) geef de `process_count` op, evenals `node_count` . De `process_count` komt overeen met het totale aantal processen dat u wilt uitvoeren voor uw taak. Dit moet doorgaans gelijk zijn aan het aantal GPU's per knooppunt vermenigvuldigd met het aantal knooppunten. Als niet is opgegeven, start Azure ML standaard één `process_count` proces per knooppunt.

Azure ML stelt de volgende omgevingsvariabelen in:
* `MASTER_ADDR` - IP-adres van de computer die het proces gaat hosten met positie 0.
* `MASTER_PORT` - Een gratis poort op de computer die het proces gaat hosten met positie 0.
* `NODE_RANK` - De rangschikking van het knooppunt voor training met meerdere knooppunt. De mogelijke waarden zijn 0 tot (totaal aantal knooppunten - 1).
* `WORLD_SIZE` - Het totale aantal processen. Dit moet gelijk zijn aan het totale aantal apparaten (GPU) dat wordt gebruikt voor gedistribueerde training.
* `RANK` - De (globale) rangschikking van het huidige proces. De mogelijke waarden zijn 0 tot (wereldgrootte - 1).
* `LOCAL_RANK` - De lokale (relatieve) rangschikking van het proces binnen het knooppunt. De mogelijke waarden zijn 0 tot (aantal processen op het knooppunt - 1).

Omdat de vereiste omgevingsvariabelen voor u worden [](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) ingesteld door Azure ML, kunt u de standaard initialisatiemethode voor omgevingsvariabelen gebruiken om de procesgroep in uw trainingscode te initialiseren.

Met het volgende codefragment wordt een PyTorch-taak met twee knooppunts en 2 processen per knooppunt geconfigureerd:
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
> Als u deze optie wilt gebruiken voor training met meerdere processen per knooppunt, moet u de Python SDK van Azure ML >= 1.22.0 gebruiken, zoals is geïntroduceerd `process_count` in 1.22.0.

> [!TIP]
> Als uw trainingsscript informatie zoals lokale rangschikking of positie als scriptargumenten doorstaat, kunt u verwijzen naar de omgevingsvariabelen in de argumenten: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Starten per knooppunt met `torch.distributed.launch`
PyTorch biedt een hulpprogramma voor starten in [het programma.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) dat gebruikers kunnen gebruiken om meerdere processen per knooppunt te starten. Met `torch.distributed.launch` de module worden meerdere trainingsprocessen op elk van de knooppunten voortgebracht.

In de volgende stappen wordt gedemonstreerd hoe u een PyTorch-taak configureert met een launcher per knooppunt in Azure ML, zodat het equivalent van het uitvoeren van de volgende opdracht wordt bereikt:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Geef de `torch.distributed.launch` opdracht op voor de parameter van de `command` `ScriptRunConfig` constructor. Azure ML zal deze opdracht uitvoeren op elk knooppunt van uw trainingscluster. `--nproc_per_node` moet kleiner zijn dan of gelijk zijn aan het aantal GPU's dat beschikbaar is op elk knooppunt. `MASTER_ADDR`, en zijn allemaal ingesteld door Azure ML, zodat u gewoon naar de omgevingsvariabelen in de `MASTER_PORT` `NODE_RANK` opdracht kunt verwijzen. Azure ML stelt `MASTER_PORT` in op 6105, maar u kunt indien nodig een andere waarde doorgeven aan het argument van `--master_port` de `torch.distributed.launch` opdracht. (Met het startprogramma worden de omgevingsvariabelen opnieuw ingesteld.)
2. Maak een `PyTorchConfiguration` en geef de `node_count` op. U hoeft niet in te stellen omdat Azure ML standaard één proces per knooppunt start, waarmee de opgegeven `process_count` startopdracht wordt uitgevoerd.

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

Zie Distributed [PyTorch with DistributedDataParallel (Gedistribueerde PyTorch met DistributedDataParallel)](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel)voor een volledige zelfstudie over het uitvoeren van gedistribueerde PyTorch op Azure ML.

### <a name="troubleshooting"></a>Problemen oplossen

* **Horovod** is afgesloten: In de meeste gevallen is er een onderliggende uitzondering in een van de processen waardoor Horovod is afgesloten als u de fout 'AbortedError: Horovod is uitgeschakeld' tegenkomt. Elke classificatie in de MPI-taak krijgt een eigen toegewezen logboekbestand in Azure ML. Deze logboeken hebben de naam `70_driver_logs`. In het geval van gedistribueerde trainingen worden de logboeknamen aangevuld met het achtervoegsel `_rank` om het onderscheiden van de logboeken gemakkelijker te maken. Als u de exacte fout wilt vinden die ervoor heeft gezorgd dat Horovod is afgesloten, bekijkt u alle logboekbestanden en kijkt u naar aan het einde van `Traceback` de driver_log bestanden. Een van deze bestanden geeft u de werkelijke onderliggende uitzondering. 

## <a name="export-to-onnx"></a>Exporteren naar ONNX

Converteer uw getrainde PyTorch-model naar de ONNX-indeling om deferentie met de [ONNX Runtime](concept-onnx.md)te optimaliseren. De deferentie, of modelscore, is de fase waarin het geïmplementeerde model wordt gebruikt voor voorspelling, meestal op productiegegevens. Zie de [zelfstudie](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) voor een voorbeeld.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een neuraal deep learning-netwerk getraind en geregistreerd met behulp van PyTorch op Azure Machine Learning. Als u wilt weten hoe u een model implementeert, gaat u verder met ons artikel over modelimplementatie.

* [Hoe en waar u modellen implementeert](how-to-deploy-and-where.md)
* [Metrische gegevens van de run bijhouden tijdens de training](how-to-log-view-metrics.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentiearchitectuur voor gedistribueerde deep learning-training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
