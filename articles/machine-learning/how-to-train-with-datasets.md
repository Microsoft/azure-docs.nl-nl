---
title: Trainen met machine learning gegevenssets
titleSuffix: Azure Machine Learning
description: Leer hoe u uw gegevens beschikbaar maakt voor uw lokale of externe rekenkracht voor modeltraining met Azure Machine Learning gegevenssets.
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
ms.openlocfilehash: edb7ebc94d2706d1bf20db8ed9a869107163ff8d
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387986"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Modellen trainen met Azure Machine Learning gegevenssets 

In dit artikel leert u hoe u kunt werken met Azure Machine Learning [gegevenssets om](/python/api/azureml-core/azureml.core.dataset%28class%29) de machine learning trainen.  U kunt gegevenssets gebruiken in uw lokale of externe rekendoel zonder dat u zich zorgen hoeft te maken over verbindingsreeksen of gegevenspaden. 

Azure Machine Learning-gegevenssets bieden een naadloze integratie met Azure Machine Learning-trainingsfunctionaliteit zoals [ScriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig) [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive) [en Azure Machine Learning pijplijnen.](./how-to-create-machine-learning-pipelines.md)

Als u er niet klaar voor bent om uw gegevens beschikbaar te maken voor modeltraining, maar uw gegevens in uw notebook wilt laden voor gegevensverkenning, bekijkt u hoe u de gegevens in uw [gegevensset verkent.](how-to-create-register-datasets.md#explore-data) 

## <a name="prerequisites"></a>Vereisten

Als u gegevenssets wilt maken en trainen, hebt u het volgende nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een [Azure Machine Learning werkruimte](how-to-manage-workspace.md).

* De [Azure Machine Learning SDK](/python/api/overview/azure/ml/install) voor Python geïnstalleerd (>= 1.13.0), die het pakket `azureml-datasets` bevat.

> [!Note]
> Sommige gegevenssetklassen hebben afhankelijkheden van het [pakket azureml-dataprep.](https://pypi.org/project/azureml-dataprep/) Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Gegevenssets gebruiken in machine learning trainingsscripts

Als u gestructureerde gegevens hebt die nog niet zijn geregistreerd als gegevensset, maakt u een TabularDataset en gebruikt u deze rechtstreeks in uw trainingsscript voor uw lokale of externe experiment.

In dit voorbeeld maakt u een niet-geregistreerde [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) en geeft u deze op als een scriptargument in het [ScriptRunConfig-object](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) voor training. Zie Gegevenssets registreren in uw werkruimte als u deze TabularDataset opnieuw wilt gebruiken met andere experimenten in [uw werkruimte.](how-to-create-register-datasets.md#register-datasets)

### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

Met de volgende code wordt een niet-geregistreerde TabularDataset gemaakt op basis van een web-URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset-objecten bieden de mogelijkheid om de gegevens in uw TabularDataset te laden in een pandas- of Spark DataFrame, zodat u kunt werken met vertrouwde gegevensvoorbereidings- en trainingsbibliotheken zonder dat u uw notebook verlaten.

### <a name="access-dataset-in-training-script"></a>Toegang tot gegevensset in trainingsscript

Met de volgende code configureert u een scriptargument dat u opgeeft wanneer u de `--input-data` trainingsrun configureert (zie de volgende sectie). Wanneer de tabellaire gegevensset wordt doorgegeven als de argumentwaarde, wordt deze door Azure ML opgelost in de id van de gegevensset, die u vervolgens kunt gebruiken om toegang te krijgen tot de gegevensset in uw trainingsscript (zonder dat u de naam of id van de gegevensset in uw script moet coderen). Vervolgens wordt de methode gebruikt om die gegevensset in een pandas-gegevensframe te laden voor verdere gegevensverkenning en [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) voorbereiding voorafgaand aan de training.

> [!Note]
> Als uw oorspronkelijke gegevensbron NaN, lege tekenreeksen of lege waarden bevat, worden deze waarden vervangen als `to_pandas_dataframe()` een *Null-waarde* wanneer u gebruikt.

Als u de voorbereide gegevens wilt laden in een nieuwe gegevensset van een pandas-gegevensframe in het geheugen, schrijft u de gegevens naar een lokaal bestand, zoals een parquet, en maakt u een nieuwe gegevensset op basis van dat bestand. Meer informatie over [het maken van gegevenssets.](how-to-create-register-datasets.md)

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

### <a name="configure-the-training-run"></a>De trainingsrun configureren

Er [wordt een ScriptRunConfig-object](/python/api/azureml-core/azureml.core.scriptrun) gebruikt om de trainingsuitloop te configureren en te verzenden.

Met deze code maakt u een ScriptRunConfig-object, `src` , dat

* Een scriptmap voor uw scripts. Alle bestanden in deze map worden naar de clusterknooppunten geüpload voor uitvoering.
* Het trainingsscript, *train_titanic.py.*
* De invoerset voor training, `titanic_ds` , als scriptargument. Azure ML lost dit op in de bijbehorende id van de gegevensset wanneer deze wordt doorgegeven aan uw script.
* Het rekendoel voor de run.
* De omgeving voor de run.

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

## <a name="mount-files-to-remote-compute-targets"></a>Bestanden aan externe rekendoelen toevoegen

Als u niet-gestructureerde gegevens hebt, maakt u een [FileDataset](/python/api/azureml-core/azureml.data.filedataset) en kunt u uw gegevensbestanden mounten of downloaden om ze beschikbaar te maken voor uw externe rekendoel voor training. Meer informatie over het gebruik van [mount vs. download](#mount-vs-download) voor uw experimenten voor externe training. 

In het volgende voorbeeld: 

* Hiermee maakt u een invoerbestandGegevensset, `mnist_ds` , voor uw trainingsgegevens.
* Hiermee geeft u op waar trainingsresultaten moeten worden geschreven en wordt het promoveren van deze resultaten als een FileDataset opgegeven.
* De invoerset wordt aan het rekendoel bevestigd.

> [!Note]
> Als u een aangepaste Docker-basisafbeelding gebruikt, moet u fuse installeren via als een afhankelijkheid om de gegevensset `apt-get install -y fuse` te kunnen laten werken. Meer informatie over het [bouwen van een aangepaste build-afbeelding.](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)

Zie How to [configure a training run with data input and output (Een trainingsuitvoer configureren met gegevensinvoer en -uitvoer) voor het notebookvoorbeeld.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb)

### <a name="create-a-filedataset"></a>Een FileDataset maken

In het volgende voorbeeld wordt een niet-geregistreerde FileDataset gemaakt op `mnist_data` basis van web-URL's. Deze FileDataset is de invoergegevens voor uw trainingsrun.

Meer informatie over [het maken van gegevenssets uit](how-to-create-register-datasets.md) andere bronnen.

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
### <a name="where-to-write-training-output"></a>Waar u trainingsuitvoer schrijft

U kunt opgeven waar u uw trainingsresultaten wilt schrijven met een [OutputFileDatasetConfig-object](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). 

Met OutputFileDatasetConfig-objecten kunt u het volgende doen: 

* De uitvoer van een uitvoer van een run wordt naar de door u opgegeven cloudopslag geleid of geüpload.
* Sla de uitvoer als een FileDataset op in deze ondersteunde opslagtypen:
    * Azure Blob
    * Azure-bestandsshare
    * Azure Data Lake Storage generaties 1 en 2
* Houd de gegevenslijn bij tussen trainings runs.

De volgende code geeft aan dat trainingsresultaten moeten worden opgeslagen als een FileDataset in de map in de `outputdataset` standaard blob-gegevensopslag, `def_blob_store` . 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>De trainingsrun configureren

We raden u aan de gegevensset door te geven als argument bij het maken van een mounting via de `arguments` parameter van de `ScriptRunConfig` constructor. Als u dit doet, krijgt u het gegevenspad (het bevestigingspunt) in uw trainingsscript via argumenten. Op deze manier kunt u hetzelfde trainingsscript gebruiken voor lokale debuggen en externe training op elk cloudplatform.

In het volgende voorbeeld wordt een ScriptRunConfig gemaakt die via de FileDataset wordt door `arguments` geven. Nadat u de run hebt verzenden, worden de gegevensbestanden naar de gegevensset verwezen aan het rekendoel en worden trainingsresultaten opgeslagen in de opgegeven map in de `mnist_ds` `outputdataset` standaardgegevensstore.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Eenvoudig trainingsscript

Het volgende script wordt verzonden via scriptRunConfig. De gegevensset wordt gelezen als invoer en het bestand wordt naar de map in de `mnist_ds ` `outputdataset` standaardblobgegevensopslag geschreven, `def_blob_store` .

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Mount vs download

Het toevoegen of downloaden van bestanden van elke indeling wordt ondersteund voor gegevenssets die zijn gemaakt op basis van Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. 

Wanneer u **een gegevensset** koppelt, koppelt u de bestanden waarnaar wordt verwezen door de gegevensset aan een map (koppelpunt) en maakt u deze beschikbaar op het rekendoel. Mounting wordt ondersteund voor linux-gebaseerde berekeningen, Azure Machine Learning Compute, virtuele machines en HDInsight. 

Wanneer u **een gegevensset** downloadt, worden alle bestanden waarnaar wordt verwezen door de gegevensset gedownload naar het rekendoel. Downloaden wordt ondersteund voor alle rekentypen. 

Als uw script alle bestanden verwerkt waarnaar wordt verwezen door de gegevensset en uw rekenschijf geschikt is voor uw volledige gegevensset, wordt het downloaden aanbevolen om de overhead van streaminggegevens van opslagservices te voorkomen. Als uw gegevensgrootte groter is dan de grootte van de rekenschijf, is downloaden niet mogelijk. Voor dit scenario raden we u aan te worden bevestigd, omdat alleen de gegevensbestanden die door uw script worden gebruikt, worden geladen op het moment van verwerking.

De volgende code wordt aan `dataset` de map temp op `mounted_path`

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

## <a name="get-datasets-in-machine-learning-scripts"></a>Gegevenssets in machine learning scripts

Geregistreerde gegevenssets zijn zowel lokaal als extern toegankelijk op rekenclusters zoals de Azure Machine Learning compute. Als u toegang wilt krijgen tot uw geregistreerde gegevensset tussen experimenten, gebruikt u de volgende code om toegang te krijgen tot uw werkruimte en de gegevensset op te halen die is gebruikt in de eerder verzonden run. De methode in de klasse retourneert standaard de meest recente versie van de [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) `Dataset` gegevensset die is geregistreerd bij de werkruimte.

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

## <a name="access-source-code-during-training"></a>Broncode openen tijdens de training

Azure Blob Storage heeft hogere doorvoersnelheden dan een Azure-bestands share en wordt geschaald naar grote aantallen taken die parallel worden gestart. Daarom raden we u aan om uw runs te configureren voor het gebruik van Blob Storage voor het overdragen van broncodebestanden.

In het volgende codevoorbeeld wordt in de uitvoeringsconfiguratie aangegeven welke blob-gegevensopslag moet worden gebruikt voor broncodeoverdrachten.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebookvoorbeelden

+ Zie de notebooks voor gegevenssets voor aanvullende voorbeelden en [concepten van gegevenssets.](https://aka.ms/dataset-tutorial)
+ Zie hoe u [gegevenssets parametriseert in uw ML-pijplijnen.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)

## <a name="troubleshooting"></a>Problemen oplossen

* **Initialisatie van gegevensset is mislukt: Er** is een time-out voor het moment dat het bevestigingspunt gereed is: 
  * Als u geen regels voor [](../virtual-network/network-security-groups-overview.md) uitgaande netwerkbeveiligingsgroep hebt en , update en de afhankelijkheden ervan gebruiken voor de meest recente versie van de specifieke secundaire versie, of als u deze gebruikt in een uitvoering, maakt u uw omgeving opnieuw zodat de meest recente patch met de oplossing kan worden `azureml-sdk>=1.12.0` `azureml-dataset-runtime` geïnstalleerd. 
  * Als u `azureml-sdk<1.12.0` gebruikt, upgradet u naar de nieuwste versie.
  * Als u uitgaande NSG-regels hebt, moet u ervoor zorgen dat er een uitgaande regel is die al het verkeer voor de servicetag `AzureResourceMonitor` toestaat.

### <a name="overloaded-azurefile-storage"></a>Overbelaste AzureFile-opslag

Als er een foutbericht wordt `Unable to upload project files to working directory in AzureFile because the storage is overloaded` weergegeven, moet u de volgende tijdelijke oplossingen toepassen.

Als u een bestands share gebruikt voor andere workloads, zoals gegevensoverdracht, is het aan teraden om blobs te gebruiken, zodat de bestands share gratis kan worden gebruikt voor het verzenden van runs. U kunt de workload ook splitsen tussen twee verschillende werkruimten.

### <a name="passing-data-as-input"></a>Gegevens doorgeven als invoer

*  **TypeError: FileNotFound:** bestand of map bestaat niet: deze fout treedt op als het bestandspad dat u opricht, zich niet bevindt op de locatie van het bestand. U moet ervoor zorgen dat de manier waarop u naar het bestand verwijst consistent is met waar u uw gegevensset aan uw rekendoel hebt bevestigd. Om een deterministische status te garanderen, raden we u aan het abstracte pad te gebruiken bij het aan een rekendoel vast te stellen gegevensset. In de volgende code wordt bijvoorbeeld de gegevensset onder de hoofdmap van het bestandssysteem van het rekendoel, , `/tmp` geplaatst. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Als u de voorlopende slash '/' niet opvoegt, moet u de werkmap vooraf laten gaan door het rekendoel om aan te geven waar u de gegevensset wilt `/mnt/batch/.../tmp/dataset` monteren.


## <a name="next-steps"></a>Volgende stappen

* [Automatisch trainen machine learning modellen](how-to-auto-train-remote.md) met TabularDatasets.

* [Modellen voor afbeeldingsclassificatie](https://aka.ms/filedataset-samplenotebook) trainen met FileDatasets.

* [Train met gegevenssets met behulp van pijplijnen](./how-to-create-machine-learning-pipelines.md).
