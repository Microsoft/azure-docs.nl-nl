---
title: Gegevens verplaatsen in ML-pijplijnen
titleSuffix: Azure Machine Learning
description: Leer hoe Azure Machine Learning pijplijnen gegevens opnemen en hoe u gegevens beheert en verplaatst tussen pijplijnstappen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: ae041bd8780524d24c360412232ec77ae60aa3c4
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884710"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Gegevens verplaatsen naar en tussen ML-pijplijnstappen (Python)

Dit artikel bevat code voor het importeren, transformeren en verplaatsen van gegevens tussen stappen in Azure Machine Learning pijplijn. Zie Access data in Azure storage services (Toegang tot gegevens in Azure Storage-services) Azure Machine Learning overzicht van hoe gegevens [in azure-opslagservices werken.](how-to-access-data.md) Zie Wat zijn Azure Machine Learning pijplijnen? voor de voordelen en structuur [van Azure Machine Learning pijplijnen.](concept-ml-pipelines.md)

In dit artikel wordt het volgende beschreven:

- Objecten `Dataset` gebruiken voor bestaande gegevens
- Toegang tot gegevens in uw stappen
- Gegevens `Dataset` splitsen in subsets, zoals trainings- en validatiesubsets
- Objecten `OutputFileDatasetConfig` maken om gegevens over te dragen naar de volgende pijplijnstap
- Objecten `OutputFileDatasetConfig` gebruiken als invoer voor pijplijnstappen
- Nieuwe objecten `Dataset` maken van waar u deze wilt blijven `OutputFileDatasetConfig` gebruiken

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

- De [Azure Machine Learning-SDK voor Python](/python/api/overview/azure/ml/intro)of toegang tot [Azure Machine Learning-studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte.
  
  Maak [een Azure Machine Learning werkruimte of](how-to-manage-workspace.md) gebruik een bestaande werkruimte via de Python SDK. Importeer `Workspace` de klasse en en laad uw `Datastore` abonnementsgegevens uit het bestand `config.json` met behulp van de functie `from_config()` . Deze functie zoekt standaard naar het JSON-bestand in de huidige map, maar u kunt ook een padparameter opgeven om naar het bestand te wijzen met behulp van `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Een aantal bestaande gegevens. In dit artikel wordt het gebruik van een [Azure Blob-container kort beschreven.](../storage/blobs/storage-blobs-overview.md)

- Optioneel: Een bestaande machine learning pijplijn, zoals wordt beschreven in Create [and run machine learning pipelines with Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)(Pijplijnen maken en uitvoeren met Azure Machine Learning SDK).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Objecten `Dataset` gebruiken voor bestaande gegevens 

De voorkeursmethode voor het opnemen van gegevens in een pijplijn is het gebruik van een [gegevenssetobject.](/python/api/azureml-core/azureml.core.dataset%28class%29) `Dataset` -objecten vertegenwoordigen permanente gegevens die beschikbaar zijn in een werkruimte.

Er zijn veel manieren om objecten te maken en `Dataset` te registreren. Gegevenssets in tabelvorm zijn voor gegevens met scheidingstekens die beschikbaar zijn in een of meer bestanden. Bestandsgegevenssets zijn voor binaire gegevens (zoals afbeeldingen) of voor gegevens die u gaat parseren. De eenvoudigste programmatische manieren om objecten te `Dataset` maken zijn het gebruik van bestaande blobs in werkruimteopslag of openbare URL's:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
```

Zie [Create Azure Machine Learning datasets](how-to-create-register-datasets.md)(Azure Machine Learning-gegevenssets maken) voor meer opties voor het maken van gegevenssets met verschillende opties en van verschillende bronnen, het registreren en controleren ervan in de Azure Machine Learning-gebruikersinterface, het begrijpen van de interactie tussen gegevensgrootte en rekencapaciteit en het versien ervan. 

### <a name="pass-datasets-to-your-script"></a>Gegevenssets doorgeven aan uw script

Gebruik de -methode van het object om het pad van de gegevensset door te geven `Dataset` aan uw `as_named_input()` script. U kunt het resulterende object aan uw script doorgeven als argument of door het argument voor uw pijplijnscript te gebruiken om de gegevensset op `DatasetConsumptionConfig` te halen met behulp van `inputs` `Run.get_context().input_datasets[]` .

Zodra u een benoemde invoer hebt gemaakt, kunt u de toegangsmodus `as_mount()` kiezen: of `as_download()` . Als uw script alle bestanden in uw gegevensset verwerkt en de schijf op uw rekenresource groot genoeg is voor de gegevensset, is de downloadtoegangsmodus de betere keuze. De downloadtoegangsmodus voorkomt de overhead van het streamen van de gegevens tijdens runtime. Als uw script toegang heeft tot een subset van de gegevensset of als het script te groot is voor uw rekenkracht, gebruikt u de modus voor het openen van de gegevensset. Lees Voor meer informatie Mount [vs. Download](./how-to-train-with-datasets.md#mount-vs-download)

Ga als volgende te werk om een gegevensset door te geven aan uw pijplijnstap:

1. Gebruik `TabularDataset.as_named_input()` of `FileDataset.as_named_input()` (geen 's' aan het einde) om een object te `DatasetConsumptionConfig` maken
1. Gebruik `as_mount()` of om de `as_download()` toegangsmodus in te stellen
1. Geef de gegevenssets door aan uw pijplijnstappen met behulp van het `arguments` argument of `inputs`

In het volgende fragment ziet u het algemene patroon voor het combineren van deze stappen binnen de `PythonScriptStep` constructor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> U moet de waarden voor al deze argumenten (dat wil zeggen, , , en ) vervangen `"train_data"` door uw eigen `"train.py"` `cluster` `iris_dataset` gegevens. Het bovenstaande fragment toont alleen de vorm van de aanroep en maakt geen deel uit van een Microsoft-voorbeeld. 

U kunt ook methoden zoals en gebruiken om meerdere invoer te maken of de hoeveelheid gegevens te verminderen `random_split()` `take_sample()` die aan uw pijplijnstap wordt doorgegeven:

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

### <a name="access-datasets-within-your-script"></a>Toegang tot gegevenssets in uw script

Benoemde invoer voor uw pijplijnstapscript is beschikbaar als een woordenlijst binnen het `Run` -object. Haal het actieve `Run` object op met behulp van en haal vervolgens de `Run.get_context()` woordenlijst met benoemde invoer op met behulp van `input_datasets` . Als u het `DatasetConsumptionConfig` -object hebt doorgegeven met behulp van `arguments` het argument in plaats van het argument , kunt u de gegevens openen met behulp van `inputs` `ArgParser` code. Beide technieken worden in het volgende fragment gedemonstreerd.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()],
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

De doorgegeven waarde is het pad naar de gegevenssetbestand(en).

Het is ook mogelijk om rechtstreeks toegang te krijgen tot een `Dataset` geregistreerde. Omdat geregistreerde gegevenssets permanent zijn en worden gedeeld in een werkruimte, kunt u ze rechtstreeks ophalen:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> De voorgaande codefragmenten tonen de vorm van de aanroepen en maken geen deel uit van een Microsoft-voorbeeld. U moet de verschillende argumenten vervangen door waarden uit uw eigen project.

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Gebruiken `OutputFileDatasetConfig` voor tussenliggende gegevens

Hoewel objecten alleen permanente gegevens vertegenwoordigen, kunnen objecten worden gebruikt voor tijdelijke gegevensuitvoer van `Dataset` [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) pijplijnstappen en permanente uitvoergegevens.  `OutputFileDatasetConfig` ondersteunt het schrijven van gegevens naar blobopslag, bestandsshare, adlsgen1 of adlsgen2. Het ondersteunt zowel de modus voor het monteren als de uploadmodus. In de modus voor het monteren worden bestanden die naar de map zijn geschreven, permanent opgeslagen wanneer het bestand wordt gesloten. In de uploadmodus worden bestanden die naar de uitvoermap worden geschreven, geüpload aan het einde van de taak. Als de taak mislukt of wordt geannuleerd, wordt de uitvoermap niet geüpload.

 `OutputFileDatasetConfig` Het standaardgedrag van het object is het schrijven naar de standaardgegevensstore van de werkruimte. Geef uw `OutputFileDatasetConfig` objecten door aan uw met de parameter `PythonScriptStep` `arguments` .

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

U kunt ervoor kiezen om de inhoud van uw object aan het einde van een run `OutputFileDatasetConfig` te uploaden. In dat geval gebruikt u de functie samen met uw -object en geeft u op of bestaande bestanden in de bestemming `as_upload()` `OutputFileDatasetConfig` moeten worden overschreven. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=(blob_store, 'outputdataset')).as_upload(overwrite=False)
```

> [!NOTE]
> Gelijktijdige schrijf schrijft naar een `OutputFileDatasetConfig` mislukt. Probeer niet één tegelijk `OutputFileDatasetConfig` te gebruiken. Deel geen enkele in een situatie met meerdere processen, zoals `OutputFileDatasetConfig` bij het gebruik van gedistribueerde training. 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Gebruiken `OutputFileDatasetConfig` als uitvoer van een trainingsstap

In de `PythonScriptStep`van uw pijplijn kunt u de beschikbare uitvoerpaden ophalen met behulp van de argumenten van het programma. Als dit de eerste stap is en hiermee de uitvoergegevens worden geïnitialiseerd, moet u de map in het opgegeven pad maken. U kunt vervolgens de bestanden schrijven die u wilt bevatten in de `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Lezen `OutputFileDatasetConfig` als invoer voor niet-initiële stappen

Nadat de eerste pijplijnstap enkele gegevens naar het pad schrijft en deze een uitvoer van die eerste stap wordt, kunnen deze worden gebruikt als invoer `OutputFileDatasetConfig` voor een latere stap. 

In de volgende code: 

* `step1_output_data` geeft aan dat de uitvoer van de PythonScriptStep wordt geschreven naar het `step1` ADLS Gen 2-gegevensstore, `my_adlsgen2` in de modus voor uploadtoegang. Meer informatie over het instellen [van rolmachtigingen](how-to-access-data.md#azure-data-lake-storage-generation-2) om gegevens terug te schrijven naar ADLS Gen 2-gegevensstores. 

* Nadat is voltooid en de uitvoer is geschreven naar de bestemming aangegeven `step1` door , is stap `step1_output_data` 2 gereed voor gebruik als `step1_output_data` invoer. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Objecten `OutputFileDatasetConfig` registreren voor hergebruik

Als u uw beschikbaar wilt maken langer dan de duur van uw experiment, registreert u deze in uw werkruimte om experimenten te delen `OutputFileDatasetConfig` en opnieuw te gebruiken.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>Inhoud `OutputFileDatasetConfig` verwijderen wanneer u deze niet meer nodig hebt

Azure verwijdert niet automatisch tussenliggende gegevens die zijn geschreven met `OutputFileDatasetConfig` . Om opslagkosten voor grote hoeveelheden onnodige gegevens te voorkomen, moet u het volgende doen:

* Tussenliggende gegevens programmatisch verwijderen aan het einde van een pijplijn-run, wanneer deze niet meer nodig zijn
* Blob-opslag gebruiken met een kortetermijnopslagbeleid voor tussenliggende gegevens (zie Kosten optimaliseren door Azure Blob Storage [te automatiseren)](../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) 
* Regelmatig niet-benodigde gegevens controleren en verwijderen

Zie Kosten voor Azure Machine Learning voor [meer Azure Machine Learning.](concept-plan-manage-cost.md)

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning maken](how-to-create-register-datasets.md)
* [Pijplijnen maken machine learning uitvoeren met Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)