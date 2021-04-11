---
title: Gegevens verplaatsen in ML-pijp lijnen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe Azure Machine Learning pijp lijnen gegevens opneemt en hoe u gegevens kunt beheren en verplaatsen tussen pijplijn stappen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 65e93cdeb5592eef92fe8c8261231179fae6af67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731799"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Gegevens verplaatsen naar en tussen ML-pijplijnstappen (Python)

Dit artikel bevat code voor het importeren, transformeren en verplaatsen van gegevens tussen stappen in een Azure Machine Learning-pijp lijn. Zie [toegang tot gegevens in azure Storage-services](how-to-access-data.md)voor een overzicht van de werking van gegevens in azure machine learning. Zie [Wat zijn Azure machine learning pijp lijnen?](concept-ml-pipelines.md)voor de voor delen en de structuur van Azure machine learning pijp lijnen.

In dit artikel wordt uitgelegd hoe u:

- `Dataset`Objecten gebruiken voor bestaande gegevens
- Toegang tot gegevens in uw stappen
- `Dataset`Gegevens splitsen in subsets, zoals training en validatie subsets
- `OutputFileDatasetConfig`Objecten maken voor het overdragen van gegevens naar de volgende pijplijn stap
- `OutputFileDatasetConfig`Objecten gebruiken als invoer voor pijplijn stappen
- Nieuwe `Dataset` objecten maken die `OutputFileDatasetConfig` u wilt behouden

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro)of toegang tot [Azure machine learning Studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte.
  
  [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md) of gebruik een bestaand item via de PYTHON-SDK. Importeer de `Workspace` `Datastore` -en-klasse en laad uw abonnements gegevens uit het bestand `config.json` met behulp van de functie `from_config()` . Deze functie zoekt standaard naar het JSON-bestand in de huidige map, maar u kunt ook een para meter Path opgeven om naar het bestand te verwijzen met `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Een aantal bestaande gegevens. In dit artikel wordt een kort overzicht gegeven van het gebruik van een [Azure Blob-container](../storage/blobs/storage-blobs-overview.md).

- Optioneel: een bestaande machine learning pijp lijn, zoals de pijplijn die wordt beschreven in [machine learning pijp lijnen maken en uitvoeren met Azure machine learning SDK](./how-to-create-machine-learning-pipelines.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>`Dataset`Objecten gebruiken voor bestaande gegevens 

De voorkeurs manier om gegevens op te nemen in een pijp lijn is het gebruik van een object [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29) . `Dataset` objecten vertegenwoordigen permanente gegevens die beschikbaar zijn in een werk ruimte.

Er zijn veel manieren om objecten te maken en te registreren `Dataset` . Tabellaire gegevens sets zijn voor een scheidings teken dat beschikbaar is in een of meer bestanden. Bestand gegevens sets zijn voor binaire gegevens (zoals installatie kopieën) of voor gegevens die u gaat parseren. De eenvoudigste manier om objecten te maken `Dataset` , is het gebruik van bestaande blobs in werkruimte opslag of open bare url's:

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

De door gegeven waarde is het pad naar de bestand (en) van de gegevensset.

Het is ook mogelijk om rechtstreeks toegang te krijgen tot een geregistreerd `Dataset` . Omdat geregistreerde gegevens sets permanent zijn en worden gedeeld in een werk ruimte, kunt u ze direct ophalen:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> De voor gaande fragmenten tonen de vorm van de aanroepen en maken geen deel uit van een micro soft-voor beeld. U moet de verschillende argumenten vervangen door waarden uit uw eigen project.

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Gebruiken `OutputFileDatasetConfig` voor tussenliggende gegevens

Hoewel `Dataset` objecten alleen persistente gegevens vertegenwoordigen, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) kunnen object (en) worden gebruikt voor tijdelijke gegevens uitvoer van pijplijn stappen **en** permanente uitvoer gegevens. `OutputFileDatasetConfig` biedt ondersteuning voor het schrijven van gegevens naar Blob Storage, file share, adlsgen1 of adlsgen2. Het ondersteunt zowel de koppel modus als de upload modus. In de modus koppelen worden bestanden die naar de gekoppelde Directory zijn geschreven, permanent opgeslagen wanneer het bestand wordt gesloten. In de upload modus worden bestanden die naar de uitvoermap worden geschreven, aan het einde van de taak geüpload. Als de taak mislukt of wordt geannuleerd, wordt de uitvoermap niet geüpload.

 `OutputFileDatasetConfig` het standaard gedrag van het object is om te schrijven naar de standaard gegevens opslag van de werk ruimte. Geef uw `OutputFileDatasetConfig` objecten door aan uw `PythonScriptStep` met de `arguments` para meter.

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

U kunt ervoor kiezen om de inhoud van uw `OutputFileDatasetConfig` object te uploaden aan het einde van een run. In dat geval gebruikt u de `as_upload()` functie samen met uw `OutputFileDatasetConfig` object en geeft u op of bestaande bestanden in de bestemming moeten worden overschreven. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=(blob_store, 'outputdataset')).as_upload(overwrite=False)
```

> [!NOTE]
> Gelijktijdige schrijf bewerkingen naar een `OutputFileDatasetConfig` mislukt. Probeer geen enkel gelijktijdig te gebruiken `OutputFileDatasetConfig` . Deel geen enkele `OutputFileDatasetConfig` in een situatie met meerdere processen, zoals bij gebruik van gedistribueerde training. 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Gebruiken `OutputFileDatasetConfig` als uitvoer van een trainings stap

In de `PythonScriptStep`van uw pijplijn kunt u de beschikbare uitvoerpaden ophalen met behulp van de argumenten van het programma. Als dit de eerste stap is en hiermee de uitvoergegevens worden geïnitialiseerd, moet u de map in het opgegeven pad maken. Vervolgens kunt u de bestanden die u wilt opnemen in de opslaan `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Lezen `OutputFileDatasetConfig` als invoer van niet-eerste stappen

Nadat de eerste pijplijn stap enkele gegevens naar het `OutputFileDatasetConfig` pad heeft geschreven en deze een uitvoer van die eerste stap wordt, kan deze worden gebruikt als invoer voor een latere stap. 

In de volgende code: 

* `step1_output_data` geeft aan dat de uitvoer van de PythonScriptStep, `step1` wordt geschreven naar het gegevens archief van ADLS gen 2 `my_adlsgen2` in de modus voor het uploaden van toegang. Meer informatie over het [instellen van rolmachtigingen](how-to-access-data.md#azure-data-lake-storage-generation-2) voor het terugschrijven van gegevens naar ADLS gen 2-gegevens opslag. 

* Nadat `step1` de uitvoering is voltooid en de uitvoer is geschreven naar de bestemming aangegeven door `step1_output_data` , is step2 klaar om te worden gebruikt `step1_output_data` als invoer. 

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

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>`OutputFileDatasetConfig`Objecten registreren voor hergebruik

Als u `OutputFileDatasetConfig` langer dan de duur van uw experiment beschikbaar wilt maken, registreert u deze voor uw werk ruimte om te delen en opnieuw te gebruiken in experimenten.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>`OutputFileDatasetConfig`Inhoud verwijderen wanneer deze niet meer nodig is

Tussenliggende gegevens die zijn geschreven met, worden niet automatisch door Azure verwijderd `OutputFileDatasetConfig` . Om opslag kosten te voor komen voor grote hoeveel heden gegevens die niet meer nodig zijn, moet u:

* Verwijder programmatisch tussenliggende gegevens aan het einde van een pijplijn uitvoering, wanneer deze niet meer nodig is
* Blob-opslag gebruiken met een opslag beleid voor de korte termijn voor tussenliggende gegevens (Zie [kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren](../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)) 
* Gegevens die niet meer nodig zijn, regel matig controleren en verwijderen

Zie [kosten plannen en beheren voor Azure machine learning](concept-plan-manage-cost.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md)
* [machine learning-pijp lijnen maken en uitvoeren met Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)