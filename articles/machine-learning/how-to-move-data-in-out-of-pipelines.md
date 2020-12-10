---
title: Gegevens verplaatsen in ML-pijp lijnen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe Azure Machine Learning pijp lijnen gegevens opneemt en hoe u gegevens kunt beheren en verplaatsen tussen pijplijn stappen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 8a5663df590e0f617f8049f0201e6e508351c755
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027574"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Gegevens verplaatsen naar en tussen ML-pijplijnstappen (Python)



Dit artikel bevat code voor het importeren, transformeren en verplaatsen van gegevens tussen stappen in een Azure Machine Learning-pijp lijn. Zie [toegang tot gegevens in azure Storage-services](how-to-access-data.md)voor een overzicht van de werking van gegevens in azure machine learning. Zie [Wat zijn Azure machine learning pijp lijnen?](concept-ml-pipelines.md)voor de voor delen en de structuur van Azure machine learning pijp lijnen.

In dit artikel wordt uitgelegd hoe u:

- `Dataset`Objecten gebruiken voor bestaande gegevens
- Toegang tot gegevens in uw stappen
- `Dataset`Gegevens splitsen in subsets, zoals training en validatie subsets
- `PipelineData`Objecten maken voor het overdragen van gegevens naar de volgende pijplijn stap
- `PipelineData`Objecten gebruiken als invoer voor pijplijn stappen
- Nieuwe `Dataset` objecten maken die `PipelineData` u wilt behouden

> [!TIP]
> Een verbeterde ervaring voor het door geven van tijdelijke gegevens tussen pijplijn stappen en het persistent maken van uw gegevens nadat pijplijn uitvoeringen beschikbaar zijn in de open bare preview-klassen,  [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) en [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py) .  Deze klassen zijn [experimentele](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) preview-functies en kunnen op elk gewenst moment worden gewijzigd.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)of toegang tot [Azure machine learning Studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte.
  
  [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md) of gebruik een bestaand item via de PYTHON-SDK. Importeer de `Workspace` `Datastore` -en-klasse en laad uw abonnements gegevens uit het bestand `config.json` met behulp van de functie `from_config()` . Deze functie zoekt standaard naar het JSON-bestand in de huidige map, maar u kunt ook een para meter Path opgeven om naar het bestand te verwijzen met `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Een aantal bestaande gegevens. In dit artikel wordt een kort overzicht gegeven van het gebruik van een [Azure Blob-container](../storage/blobs/storage-blobs-overview.md).

- Optioneel: een bestaande machine learning pijp lijn, zoals de pijplijn die wordt beschreven in [machine learning pijp lijnen maken en uitvoeren met Azure machine learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>`Dataset`Objecten gebruiken voor bestaande gegevens 

De voorkeurs manier om gegevens op te nemen in een pijp lijn is het gebruik van een object [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) . `Dataset` objecten vertegenwoordigen permanente gegevens die beschikbaar zijn in een werk ruimte.

Er zijn veel manieren om objecten te maken en te registreren `Dataset` . Tabellaire gegevens sets zijn voor een scheidings teken dat beschikbaar is in een of meer bestanden. Bestand gegevens sets zijn voor binaire gegevens (zoals installatie kopieën) of voor gegevens die u gaat parseren. De eenvoudigste manier om objecten te maken `Dataset` , is het gebruik van bestaande blobs in werkruimte opslag of open bare url's:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Zie [Azure machine learning gegevens sets maken](how-to-create-register-datasets.md)voor meer opties voor het maken van gegevens sets met verschillende opties en uit verschillende bronnen, het registreren van de gegevens in de gebruikers interface van de Azure machine learning. 

### <a name="pass-datasets-to-your-script"></a>Gegevens sets door geven aan het script

Als u het pad van de gegevensset wilt door geven aan uw script, gebruikt u de `Dataset` methode van het object `as_named_input()` . U kunt het resulterende object door geven `DatasetConsumptionConfig` aan uw script als een argument of, met behulp van het `inputs` argument voor uw pijplijn script, u de gegevensset kunt ophalen met `Run.get_context().input_datasets[]` .

Zodra u een benoemde invoer hebt gemaakt, kunt u de toegangs modus kiezen: `as_mount()` of `as_download()` . Als uw script alle bestanden in uw gegevensset verwerkt en de schijf op de reken resource groot genoeg is voor de gegevensset, is de Download toegangs modus de beste keuze. De Download toegangs modus voor komt dat de overhead van het streamen van gegevens tijdens runtime wordt voor komen. Als uw script toegang krijgt tot een subset van de gegevensset of als deze te groot is voor uw compute, moet u de toegangs modus voor koppelen gebruiken. Lees [koppelen versus downloaden](./how-to-train-with-datasets.md#mount-vs-download) voor meer informatie.

Een gegevensset door geven aan de pijplijn stap:

1. Gebruik `TabularDataset.as_named_input()` of `FileDataset.as_named_input()` (geen ' aan het einde) om een object te maken `DatasetConsumptionConfig`
1. `as_mount()` `as_download()` De toegangs modus gebruiken of instellen
1. Geef de gegevens sets door aan de pijplijn stappen met behulp van het `arguments` of het `inputs` argument

Het volgende code fragment toont het algemene patroon van het combi neren van deze stappen in de `PythonScriptStep` constructor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> U moet de waarden voor al deze argumenten (,,, `"train_data"` en) vervangen door `"train.py"` `cluster` `iris_dataset` uw eigen gegevens. Het bovenstaande code fragment toont alleen de vorm van de oproep en maakt geen deel uit van een micro soft-voor beeld. 

U kunt ook methoden als gebruiken `random_split()` `take_sample()` om meerdere invoer te maken of de hoeveelheid gegevens te verminderen die worden door gegeven aan de pijplijn stap:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Toegang tot gegevens sets in uw script

Benoemde invoer van het script voor de pijplijn stap is beschikbaar als een woorden lijst binnen het `Run` object. Haal het actieve `Run` object op met behulp `Run.get_context()` van en haal vervolgens de woorden lijst met de naam invoer op met behulp van `input_datasets` . Als u het object hebt door gegeven `DatasetConsumptionConfig` met behulp `arguments` van het argument in plaats van het `inputs` argument, opent u de gegevens met behulp van `ArgParser` code. Beide technieken worden in het volgende code fragment getoond.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

De door gegeven waarde is het pad naar de bestand (en) van de gegevensset.

Het is ook mogelijk om rechtstreeks toegang te krijgen tot een geregistreerd `Dataset` . Omdat geregistreerde gegevens sets permanent zijn en worden gedeeld in een werk ruimte, kunt u ze direct ophalen:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> De voor gaande fragmenten tonen de vorm van de aanroepen en maken geen deel uit van een micro soft-voor beeld. U moet de verschillende argumenten vervangen door waarden uit uw eigen project.

## <a name="use-pipelinedata-for-intermediate-data"></a>Gebruiken `PipelineData` voor tussenliggende gegevens

Hoewel `Dataset` objecten persistente gegevens vertegenwoordigen, worden [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) -objecten gebruikt voor tijdelijke gegevens die worden uitgevoerd vanuit pijplijn stappen. Omdat de levens duur van een `PipelineData` object langer is dan één pijplijn stap, definieert u deze in het definitie script van de pijp lijn. Wanneer u een `PipelineData` -object maakt, moet u een naam en een gegevens opslag opgeven waarop de gegevens worden opgeslagen. Geef uw `PipelineData` object (en) aan uw `PythonScriptStep` objecten _door_ met behulp van de `arguments` `outputs` argumenten en:

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

U kunt ervoor kiezen om uw `PipelineData` object te maken met behulp van een toegangs modus waarmee u een onmiddellijke upload kunt doen. In dat geval moet u, wanneer u uw maakt `PipelineData` , `upload_mode` het `"upload"` argument instellen en gebruiken `output_path_on_compute` om het pad op te geven waarnaar u de gegevens wilt schrijven:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!NOTE]
> De voor gaande fragmenten tonen de vorm van de aanroepen en maken geen deel uit van een micro soft-voor beeld. U moet de verschillende argumenten vervangen door waarden uit uw eigen project.

> [!TIP]
> Een verbeterde ervaring voor het door geven van tussenliggende gegevens tussen pijplijn stappen is beschikbaar in de open bare preview-klasse [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . `OutputFileDatasetConfig`Zie [een pijp lijn met twee stappen maken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)voor een code voorbeeld.


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Gebruiken `PipelineData` als uitvoer van een trainings stap
Binnen de pijp lijn `PythonScriptStep` kunt u de beschik bare uitvoer paden ophalen met behulp van de argumenten van het programma. Als deze stap het eerst is en de uitvoer gegevens initialiseert, moet u de Directory maken op het opgegeven pad. Vervolgens kunt u de bestanden die u wilt opnemen in de opslaan `PipelineData` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Als u uw hebt gemaakt `PipelineData` met het `is_directory` argument ingesteld op `True` , zou het voldoende zijn om alleen de oproep uit te voeren `os.makedirs()` . vervolgens zou u de gewenste bestanden naar het pad kunnen schrijven. Zie de [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) -referentie documentatie voor meer informatie.


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Lezen `PipelineData` als invoer van niet-eerste stappen

Nadat de eerste pijplijn stap enkele gegevens naar het `PipelineData` pad heeft geschreven en deze een uitvoer van die eerste stap wordt, kan deze worden gebruikt als invoer voor een latere stap:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

De waarde van een `PipelineData` invoer is het pad naar de vorige uitvoer. 

> [!NOTE]
> De voor gaande fragmenten tonen de vorm van de aanroepen en maken geen deel uit van een micro soft-voor beeld. U moet de verschillende argumenten vervangen door waarden uit uw eigen project.

> [!TIP]
> Een verbeterde ervaring voor het door geven van tussenliggende gegevens tussen pijplijn stappen is beschikbaar in de open bare preview-klasse [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . `OutputFileDatasetConfig`Zie [een pijp lijn met twee stappen maken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)voor een code voorbeeld.

Als, zoals eerder weer gegeven, de eerste stap een enkel bestand heeft geschreven, kan dit er als volgt uitzien: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>`PipelineData`Objecten naar `Dataset` s converteren

Als u `PipelineData` langer dan de duur van een uitvoering beschikbaar wilt maken, gebruikt u de `as_dataset()` functie ervan om deze te converteren naar een `Dataset` . U kunt vervolgens de registreren `Dataset` , waardoor het een eersteklas burger is in uw werk ruimte. Omdat uw `PipelineData` object elke keer dat de pijp lijn wordt uitgevoerd een ander pad heeft, is het raadzaam dat u instelt `create_new_version` op `True` Wanneer u een `Dataset` gemaakt van een `PipelineData` object registreert.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> Een verbeterde ervaring voor het persistent maken van uw tussenliggende gegevens buiten uw pijplijn uitvoeringen is beschikbaar met de open bare preview-klasse [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . `OutputFileDatasetConfig`Zie [een pijp lijn met twee stappen maken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)voor een code voorbeeld.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md)
* [machine learning-pijp lijnen maken en uitvoeren met Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)