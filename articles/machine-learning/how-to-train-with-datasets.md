---
title: Trainen met azureml-gegevens sets
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw gegevens beschikbaar maakt voor uw lokale of externe Compute voor ML-model training met Azure Machine Learning gegevens sets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b6ec9d7035194efc471fc06befad9822c8684a5d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685576"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Train met gegevens sets in Azure Machine Learning


In dit artikel leert u hoe u kunt werken met [Azure machine learning gegevens sets](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) in uw trainings experimenten.  U kunt gegevens sets gebruiken in uw lokale of externe Compute-doel zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden.

Azure Machine Learning gegevens sets bieden een naadloze integratie met Azure Machine Learning trainings functionaliteit, zoals [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py), [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py) en [Azure machine learning pijp lijnen](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en trainen, hebt u het volgende nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python geïnstalleerd](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0), waaronder het pakket met de azureml-gegevens sets.

> [!Note]
> Voor sommige verzamelings klassen zijn afhankelijkheden van het pakket voor [azureml-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) . Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="use-datasets-directly-in-training-scripts"></a>Gegevens sets rechtstreeks in trainings scripts gebruiken

Als u gestructureerde gegevens nog niet hebt geregistreerd als een gegevensset, maakt u een TabularDataset en gebruikt u deze rechtstreeks in uw trainings script voor uw lokale of externe experiment.

In dit voor beeld maakt u een niet-geregistreerde [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) en geeft u deze op als een script argument in het object ScriptRunConfig voor training. Zie [gegevens sets registreren in uw werk ruimte](how-to-create-register-datasets.md#register-datasets)als u deze TabularDataset met andere experimenten in uw werk ruimte wilt gebruiken.

### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

Met de volgende code wordt een niet-geregistreerde TabularDataset gemaakt van een web-URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset-objecten bieden de mogelijkheid om de gegevens in uw TabularDataset te laden in een Panda of Spark data frame, zodat u kunt werken met de vertrouwde gegevens voorbereiding en-trainings bibliotheken zonder dat u uw notebook hoeft te verlaten.

### <a name="access-dataset-in-training-script"></a>Toegang tot gegevensset in trainings script

Met de volgende code wordt een script argument geconfigureerd `--input-data` dat u opgeeft wanneer u uw trainings uitvoering configureert (zie volgende sectie). Wanneer de tabellaire gegevensset wordt door gegeven als de argument waarde, wordt die door Azure ML omgezet naar ID van de gegevensset, die u vervolgens kunt gebruiken om toegang te krijgen tot de gegevensset in uw trainings script (zonder dat u de naam of ID van de gegevensset in uw script hoeft te voorlopig hardcoderen we). Vervolgens wordt de- [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) methode gebruikt om die gegevensset te laden in een Panda data frame voor verdere gegevens exploratie en voor bereiding voordat ze worden getraind.

> [!Note]
> Als de oorspronkelijke gegevens bron NaN, lege teken reeksen of lege waarden bevat, `to_pandas_dataframe()` worden deze waarden vervangen als een *Null* -waarde.

Als u de voor bereide gegevens wilt laden in een nieuwe gegevensset vanuit een in-Memory-Panda data frame, schrijft u de gegevens naar een lokaal bestand, zoals een Parquet, en maakt u een nieuwe gegevensset vanuit dat bestand. U kunt ook gegevens sets maken op basis van lokale bestanden of paden in gegevens opslag. Meer informatie over [het maken van gegevens sets](how-to-create-register-datasets.md).

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>De trainings uitvoering configureren
Een [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun?preserve-view=true&view=azure-ml-py) -object wordt gebruikt om de uitvoering van de training te configureren en in te dienen.

Deze code maakt een ScriptRunConfig-object, `src` dat aangeeft

* Een script Directory voor uw scripts. Alle bestanden in deze map worden naar de clusterknooppunten geüpload voor uitvoering.
* Het trainings script, *train_titanic. py*.
* De invoer gegevensset voor training, `titanic_ds` als een script argument. Met Azure ML wordt dit omgezet in de bijbehorende ID van de gegevensset wanneer deze wordt door gegeven aan uw script.
* Het reken doel voor de uitvoering.
* De omgeving voor de uitvoering.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Bestanden koppelen aan externe Compute-doelen

Als u ongestructureerde gegevens hebt, maakt u een [FileDataset](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) en koppelt of downloadt u uw gegevens bestanden om ze beschikbaar te maken voor uw externe Compute-doel voor training. Meer informatie over het gebruik van [Mount en down load](#mount-vs-download) voor uw externe trainings experimenten. 

In het volgende voor beeld wordt een FileDataset gemaakt en wordt de gegevensset aan het reken doel gekoppeld door deze als een argument aan het trainings script door te geven. 

> [!Note]
> Als u een aangepaste docker-basis installatie kopie gebruikt, moet u de installatie van zekering instellen op `apt-get install -y fuse` een afhankelijkheid van het koppelen van de gegevensset. Meer informatie over het [maken van een aangepaste build-installatie kopie](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Een FileDataset maken

In het volgende voor beeld wordt een niet-geregistreerde FileDataset gemaakt op basis van web-url's. Meer informatie over [het maken van gegevens sets](how-to-create-register-datasets.md) van andere bronnen.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-training-run"></a>De trainings uitvoering configureren
Het is raadzaam om de gegevensset als argument aan te geven wanneer u een koppeling maakt via de `arguments` para meter van de `ScriptRunConfig` constructor. Als u dit doet, wordt het gegevenspad (koppelings punt) in uw trainings script via argumenten weer gegeven. Op deze manier kunt u hetzelfde trainings script gebruiken voor lokale fout opsporing en externe trainingen op elk cloud platform.

In het volgende voor beeld wordt een ScriptRunConfig gemaakt die wordt door gegeven in de FileDataset via `arguments` . Nadat u de uitvoering hebt verzonden, worden de gegevens bestanden waarnaar de gegevensset verwijst, `mnist_ds` aan het reken doel gekoppeld.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>De gegevens in uw trainings script ophalen

De volgende code laat zien hoe u de gegevens in uw script kunt ophalen.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Koppeling versus downloaden

Het koppelen of downloaden van bestanden van een indeling wordt ondersteund voor gegevens sets die zijn gemaakt op basis van Azure Blob-opslag, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. 

Wanneer u een gegevensset **koppelt** , koppelt u de bestanden waarnaar wordt verwezen door de gegevensset naar een directory (koppel punt) en maakt u deze beschikbaar op het berekenings doel. Koppelen wordt ondersteund voor op Linux gebaseerde berekeningen, waaronder Azure Machine Learning compute, virtual machines en HDInsight. 

Wanneer u een gegevensset **downloadt** , worden alle bestanden waarnaar wordt verwezen door de gegevensset gedownload naar het Compute-doel. Downloaden wordt ondersteund voor alle reken typen. 

Als uw script alle bestanden verwerkt waarnaar wordt verwezen door de gegevensset, en uw berekenings schijf kan overeenkomen met uw volledige gegevensset, wordt het downloaden aanbevolen om de overhead van streaming-gegevens van opslag services te voor komen. Als uw gegevens grootte de grootte van de berekenings schijf overschrijdt, is downloaden niet mogelijk. Voor dit scenario wordt u aangeraden te koppelen, omdat alleen de gegevens bestanden die door het script worden gebruikt, worden geladen op het moment van verwerking.

De volgende code wordt gekoppeld `dataset` aan de tijdelijke directory op `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="directly-access-datasets-in-your-script"></a>Rechtstreeks toegang tot gegevens sets in uw script

Geregistreerde gegevens sets zijn zowel lokaal als extern toegankelijk op reken clusters, zoals de Azure Machine Learning compute. Gebruik de volgende code om toegang te krijgen tot uw geregistreerde gegevensset voor experimenten en de geregistreerde gegevensset op naam te openen. De [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) methode voor de `Dataset` klasse retourneert standaard de meest recente versie van de gegevensset die is geregistreerd bij de werk ruimte.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Toegang tot de bron code tijdens de training

Azure Blob-opslag heeft hogere doorvoer snelheden dan een Azure-bestands share en schaalt naar een groot aantal taken die parallel worden gestart. Daarom is het raadzaam om uw uitvoeringen te configureren voor het gebruik van Blob-opslag voor het overdragen van broncode bestanden.

In het volgende code voorbeeld wordt de uitvoerings configuratie opgegeven die BLOB-gegevens opslag moet gebruiken voor bron code overdrachten.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Voor beelden van notebooks

+ De [notitie blokken](https://aka.ms/dataset-tutorial) van de gegevensset tonen en uitvouwen op concepten in dit artikel.
+ Bekijk hoe u [gegevens sets in uw ml-pijp lijnen kunt parametize](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="next-steps"></a>Volgende stappen

* [Machine learning modellen automatisch trainen](how-to-auto-train-remote.md) met TabularDatasets.

* [Train afbeeldings classificatie modellen](https://aka.ms/filedataset-samplenotebook) met FileDatasets.

* [Train met gegevens sets met behulp van pijp lijnen](how-to-create-your-first-pipeline.md).
