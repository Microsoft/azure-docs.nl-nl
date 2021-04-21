---
title: Azure Machine Learning-gegevenssets maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken Azure Machine Learning gegevenssets voor toegang tot uw gegevens voor machine learning experiment runs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: f47d610a24de2cfc8f1131f61afc8c8173a34376
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786617"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning-gegevenssets maken

In dit artikel leert u hoe u Azure Machine Learning maakt voor toegang tot gegevens voor uw lokale of externe experimenten met de python-SDK Azure Machine Learning python. Als u wilt weten waar gegevenssets in Azure Machine Learning algemene werkstroom voor gegevenstoegang passen, gaat u naar het artikel [Gegevens veilig](concept-data.md#data-workflow) openen.

Als u een gegevensset maakt, maakt u ook een verwijzing naar de locatie van de gegevensbron, samen met een kopie van de bijbehorende metagegevens. Omdat de gegevens op de bestaande locatie blijven, worden er geen extra opslagkosten in gebracht en loopt u geen risico op de integriteit van uw gegevensbronnen. Ook gegevenssets worden lazily geëvalueerd, wat de werkstroomprestaties versnelt. U kunt gegevenssets maken op basis van gegevensstores, openbare URL's [en Azure Open Datasets.](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)

Voor een ervaring met weinig code maakt [u Azure Machine Learning gegevenssets met de Azure Machine Learning-studio.](how-to-connect-data-ui.md#create-datasets)

Met Azure Machine Learning-gegevenssets kunt u het volgende doen:

* Bewaar één kopie van gegevens in uw opslag waarnaar wordt verwezen door gegevenssets.

* Naadloos toegang krijgen tot gegevens tijdens de modeltraining zonder dat u zich zorgen hoeft te maken over verbindingsreeksen of gegevenspaden. [Meer informatie over trainen met gegevenssets](how-to-train-with-datasets.md).

* Gegevens delen en samenwerken met andere gebruikers.

## <a name="prerequisites"></a>Vereisten

Voor het maken en werken met gegevenssets hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een [Azure Machine Learning werkruimte](how-to-manage-workspace.md).

* De [Azure Machine Learning SDK voor Python geïnstalleerd,](/python/api/overview/azure/ml/install)die het pakket azureml-datasets bevat.

    * Maak een [Azure Machine Learning compute-instantie.](how-to-create-manage-compute-instance.md)Dit is een volledig geconfigureerde en beheerde ontwikkelomgeving met geïntegreerde notebooks en de al geïnstalleerde SDK.

    **OF**

    * Werk aan uw eigen Jupyter-notebook en installeer de SDK zelf met [deze instructies.](/python/api/overview/azure/ml/install)

> [!NOTE]
> Sommige gegevenssetklassen hebben afhankelijkheden van het [pakket azureml-dataprep,](https://pypi.org/project/azureml-dataprep/) dat alleen compatibel is met 64-bits Python. Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) en CentOS (7). Als u niet-ondersteunde distributies gebruikt, volgt u deze handleiding om .NET Core 2.1 te installeren om door te gaan. [](/dotnet/core/install/linux) 

## <a name="compute-size-guidance"></a>Richtlijnen voor rekenkracht

Wanneer u een gegevensset maakt, controleert u de rekenkracht en de grootte van uw gegevens in het geheugen. De grootte van uw gegevens in de opslag is niet hetzelfde als de grootte van gegevens in een gegevensframe. Gegevens in CSV-bestanden kunnen bijvoorbeeld tot 10 x worden uitgebreid in een gegevensframe, zodat een CSV-bestand van 1 GB in een gegevensframe 10 GB kan worden. 

Als uw gegevens zijn gecomprimeerd, kunnen deze verder worden uitgebreid; 20 GB aan relatief verspreide gegevens die zijn opgeslagen in gecomprimeerde Parquet-indeling kan worden uitgebreid tot ongeveer 800 GB aan geheugen. Omdat Parquet-bestanden gegevens opslaan in een kolomindeling, hoeft u slechts ongeveer 400 GB aan geheugen te laden als u slechts de helft van de kolommen nodig hebt.

[Meer informatie over het optimaliseren van gegevensverwerking in Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Typen gegevenssets

Er zijn twee typen gegevenssets, op basis van hoe gebruikers deze gebruiken in de training; FileDatasets en TabularDatasets. Beide typen kunnen worden gebruikt in Azure Machine Learning trainingswerkstromen met betrekking tot, estimators, AutoML, hyperDrive en pijplijnen. 

### <a name="filedataset"></a>FileDataset

Een [FileDataset verwijst](/python/api/azureml-core/azureml.data.file_dataset.filedataset) naar een of meer bestanden in uw gegevensstores of openbare URL's. Als uw gegevens al zijn opschoond en klaar [](how-to-train-with-datasets.md#mount-vs-download) zijn voor gebruik in trainingsexperimenten, kunt u de bestanden downloaden of aan uw rekenkracht toevoegen als een FileDataset-object. 

We raden FileDatasets aan voor uw machine learning-werkstromen, omdat de bronbestanden elke indeling kunnen hebben, waardoor een breed scala aan machine learning scenario's mogelijk is, waaronder deep learning.

Maak een FileDataset met de [Python SDK](#create-a-filedataset) of de [Azure Machine Learning-studio](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

Een [TabularDataset vertegenwoordigt](/python/api/azureml-core/azureml.data.tabulardataset) gegevens in tabelvorm door het opgegeven bestand of de opgegeven lijst met bestanden te parseren. Dit biedt u de mogelijkheid om de gegevens te materialiseren in een pandas- of Spark DataFrame, zodat u kunt werken met vertrouwde bibliotheken voor gegevensvoorbereiding en training zonder dat u uw notebook verlaten. U kunt een object maken van `TabularDataset` .csv-, .tsv-, [.parquet-](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)en [.jsonl-bestanden](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--)en van [SQL-queryresultaten.](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)

Met TabularDatasets kunt u een tijdstempel opgeven vanuit een kolom in de gegevens of vanaf elke locatie waar de padpatroongegevens worden opgeslagen om een tijdreekseigenschap mogelijk te maken. Met deze specificatie kunt u eenvoudig en efficiënt filteren op tijd. Zie Tabular [time series-related API demo with NOAA weather data (Api-demo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)in tabelvorm met NOAA-weergegevens) voor een voorbeeld.

Maak een TabularDataset met [de Python SDK](#create-a-tabulardataset) of [Azure Machine Learning-studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> [Geautomatiseerde](concept-automated-ml.md) ML-werkstromen die zijn gegenereerd via de Azure Machine Learning-studio ondersteunen momenteel alleen TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Toegang tot gegevenssets in een virtueel netwerk

Als uw werkruimte zich in een virtueel netwerk, moet u de gegevensset configureren om validatie over te slaan. Zie Een werkruimte en gekoppelde resources beveiligen voor meer informatie over het gebruik van gegevensstores en gegevenssets in [een virtueel netwerk.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Gegevenssets maken vanuit gegevensstores

Om de gegevens toegankelijk te maken Azure Machine Learning, moeten gegevenssets worden gemaakt op basis van paden in [Azure Machine Learning](how-to-access-data.md) of web-URL's. 

> [!TIP] 
> U kunt gegevenssets rechtstreeks vanuit opslag-URL's maken met op identiteit gebaseerde gegevenstoegang. Zie Verbinding [maken met opslag met op identiteit gebaseerde gegevenstoegang (preview)](how-to-identity-based-data-access.md) voor meer informatie<br><br>
Deze mogelijkheid is een [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functie en kan op elk moment worden gewijzigd. 

 
Gegevenssets maken vanuit een gegevensstore met de Python SDK:

1. Controleer of u toegang hebt `contributor` tot `owner` de onderliggende opslagservice van uw geregistreerde Azure Machine Learning gegevensopslag. [Controleer de machtigingen voor uw opslagaccount in Azure Portal](../role-based-access-control/check-access.md).

1. Maak de gegevensset door te verwijzen naar paden in de gegevensstore. U kunt een gegevensset maken op basis van meerdere paden in meerdere gegevensstores. Er is geen harde limiet voor het aantal bestanden of de gegevensgrootte waar u een gegevensset van kunt maken. 

> [!NOTE]
> Voor elk gegevenspad worden enkele aanvragen verzonden naar de opslagservice om te controleren of het naar een bestand of map wijst. Deze overhead kan leiden tot slechtere prestaties of fouten. Een gegevensset die naar één map met 1000 bestanden binnen verwijst, wordt beschouwd als een verwijzing naar één gegevenspad. We raden u aan om gegevenssets te maken die verwijzen naar minder dan 100 paden in gegevensstores voor optimale prestaties.

### <a name="create-a-filedataset"></a>Een FileDataset maken

Gebruik de methode in de klasse om bestanden in een indeling te laden [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) `FileDatasetFactory` en een niet-geregistreerde FileDataset te maken. 

Als uw opslag zich achter een virtueel netwerk of een firewall bevinden, stelt u de parameter `validate=False` in uw methode `from_files()` in. Hiermee wordt de eerste validatiestap overgeslagen en zorgt u ervoor dat u uw gegevensset kunt maken op basis van deze beveiligde bestanden. Meer informatie over het gebruik [van gegevensstores en gegevenssets in een virtueel netwerk.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Registreer uw gegevensset om gegevenssets opnieuw te gebruiken en te delen in uw [werkruimte.](#register-datasets) 

> [!TIP] 
> Upload bestanden vanuit een lokale map en maak een FileDataset in één methode met de openbare [preview-methode, upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Deze methode is een [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functie en kan op elk moment worden gewijzigd. 
> 
>  Met deze methode worden gegevens geüpload naar uw onderliggende opslag, wat leidt tot opslagkosten. 

### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

Gebruik de methode in de klasse om bestanden in CSV- of TSV-indeling te lezen en om een [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) `TabularDatasetFactory` niet-geregistreerde TabularDataset te maken. Als u bestanden uit de .parquet-indeling wilt inlezen, gebruikt u de [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) methode . Als u uit meerdere bestanden leest, worden de resultaten geaggregeerd in één tabelweergave. 

Zie de [naslagdocumentatie voor TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) voor informatie over ondersteunde bestandsindelingen, evenals syntaxis en ontwerppatronen. 

Als uw opslag zich achter een virtueel netwerk of een firewall bevinden, stelt u de parameter `validate=False` in uw methode `from_delimited_files()` in. Hiermee wordt de eerste validatiestap overgeslagen en zorgt u ervoor dat u uw gegevensset kunt maken op basis van deze beveiligde bestanden. Meer informatie over het gebruik van [gegevensstores en gegevenssets in een virtueel netwerk.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

Met de volgende code worden de bestaande werkruimte en het gewenste gegevens opslaan op naam. Vervolgens worden de gegevensstore en bestandslocaties doorgegeven aan de `path` parameter om een nieuwe TabularDataset, te `weather_ds` maken.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Gegevensschema instellen

Wanneer u een TabularDataset maakt, worden kolomgegevenstypen standaard automatisch afgeleid. Als de afgeleide typen niet aan uw verwachtingen voldoen, kunt u uw gegevenssetschema bijwerken door kolomtypen op te geven met de volgende code. De parameter `infer_column_type` is alleen van toepassing op gegevenssets die zijn gemaakt op basis van bestanden met scheidingstekens. [Meer informatie over ondersteunde gegevenstypen.](/python/api/azureml-core/azureml.data.dataset_factory.datatype)


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Overleefde|Pclass|Name|Sex|Leeftijd|SibSp|Parch|Ticket|Tarief|Cabine|Aan de start
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Niet waar|3|Hadoed, Mr.|man|22,0|1|0|A/5 21171|7.2500||S
1|2|Waar|1|Cumings, Mevr. John|vrouwelijk|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Waar|3|Heikkinen, Miss. Laina|vrouwelijk|26.0|0|0|STON/O2. 3101282|7.9250||S

Registreer uw gegevensset om gegevenssets opnieuw te gebruiken en te delen tussen experimenten in [uw werkruimte.](#register-datasets)

## <a name="wrangle-data"></a>Wrangle-gegevens
Nadat u uw gegevensset [hebt](#register-datasets) gemaakt en geregistreerd, kunt u [](#explore-data) deze in uw notebook laden voor data-wrangling en verkenning voorafgaand aan de modeltraining. 

Als u geen data-wrangling of verkenning hoeft uit te voeren, bekijkt u hoe u gegevenssets gebruikt in uw trainingsscripts voor het verzenden van ML-experimenten in Trainen met [gegevenssets.](how-to-train-with-datasets.md)

### <a name="filter-datasets-preview"></a>Gegevenssets filteren (preview)

De filtermogelijkheden zijn afhankelijk van het type gegevensset dat u hebt. 
> [!IMPORTANT]
> Het filteren van gegevenssets met de preview-methode [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) is een [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functie en kan op elk moment worden gewijzigd. 
> 
**Voor TabularDatasets kunt** u kolommen behouden of verwijderen met de [methoden keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) en [drop_columns().](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-)

Als u rijen wilt filteren op een specifieke kolomwaarde in een TabularDataset, gebruikt u de [filter()-methode](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (preview). 

De volgende voorbeelden retourneren een niet-geregistreerde gegevensset op basis van de opgegeven expressies.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**In FileDatasets** komt elke rij overeen met een pad van een bestand, dus filteren op kolomwaarde is niet nuttig. Maar u kunt rijen [filteren()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) op metagegevens zoals CreationTime, Size, enzovoort.

De volgende voorbeelden retourneren een niet-geregistreerde gegevensset op basis van de opgegeven expressies.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

**Gelabelde gegevenssets die** zijn [gemaakt op basis van gegevenslabelprojecten](how-to-create-labeling-projects.md) zijn een speciaal geval. Deze gegevenssets zijn een type TabularDataset dat bestaat uit afbeeldingsbestanden. Voor deze typen gegevenssets kunt u [afbeeldingen filteren()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) op metagegevens en op kolomwaarden zoals `label` en `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data-preview"></a>Partitiegegevens (preview)

U kunt een gegevensset partitioneren door de parameter op te nemen bij het maken van `partitions_format` een TabularDataset of FileDataset. 

> [!IMPORTANT]
> Het maken van gegevenssetpartities is [een experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functie en kan op elk moment worden gewijzigd. 

Wanneer u een gegevensset partitioneert, worden de partitiegegevens van elk bestandspad geëxtraheerd in kolommen op basis van de opgegeven indeling. De indeling moet beginnen vanaf de positie van de eerste partitiesleutel tot het einde van het bestandspad. 

Bijvoorbeeld, op het pad waar de partitie is op afdelingsnaam en tijd; de maakt een tekenreekskolom 'Afdeling' met de waarde 'Accounts' en een datum/tijd-kolom `../Accounts/2019/01/01/data.jsonl` `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` 'PartitionDate' met de waarde `2019-01-01` .

Als uw gegevens al bestaande partities hebben en u deze indeling wilt behouden, moet u de parameter opnemen in uw methode om `partitioned_format` [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) een FileDataset te maken. 

Als u een TabularDataset wilt maken die bestaande partities behoudt, moet u de parameter opnemen `partitioned_format` in de methode [from_parquet_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) of [from_delimited_files().](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--)

Het volgende voorbeeld:
* Hiermee maakt u een FileDataset op basis van gepartities.
* Haalt de partitiesleutels op
* Hiermee maakt u een nieuwe, geïndexeerde FileDataset met behulp van
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

U kunt ook een nieuwe partitiestructuur maken voor TabularDatasets met de [methode partitions_by().](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-)

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

>[!IMPORTANT]
> TabularDataset-partities kunnen ook worden toegepast in Azure Machine Learning pijplijnen als invoer voor uw ParallelRunStep in veel modeltoepassingen. Zie een voorbeeld in de [documentatie voor de accelerator Veel modellen.](https://github.com/microsoft/solution-accelerator-many-models/blob/master/01_Data_Preparation.ipynb)

## <a name="explore-data"></a>Gegevens verkennen

Nadat u klaar bent met het wrangling van uw gegevens, kunt u uw gegevensset registreren en deze vervolgens in uw notebook laden voor gegevensverkenning voorafgaand aan de modeltraining. [](#register-datasets)

Voor FileDatasets kunt  u  uw gegevensset aan uw gegevensset toevoegen of downloaden en de Python-bibliotheken toepassen die u normaal gesproken gebruikt voor gegevensverkenning. [Meer informatie over mount vs download.](how-to-train-with-datasets.md#mount-vs-download)

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Gebruik voor TabularDatasets de methode om uw gegevens in een [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) gegevensframe weer te geven. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Overleefde|Pclass|Name|Sex|Leeftijd|SibSp|Parch|Ticket|Tarief|Cabine|Aan de start
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Niet waar|3|Hadoed, Mr.|man|22,0|1|0|A/5 21171|7.2500||S
1|2|Waar|1|Cumings, Mevr. John|vrouwelijk|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Waar|3|Heikkinen, Miss. Laina|vrouwelijk|26.0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Een gegevensset maken van pandas-gegevensframe

Als u een TabularDataset wilt maken van een pandas-dataframe in het geheugen, schrijft u de gegevens naar een lokaal bestand, zoals een CSV, en maakt u uw gegevensset op basis van dat bestand. De volgende code demonstreert deze werkstroom.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> Maak en registreer een TabularDataset vanuit een spark- of pandas-gegevensframe in het geheugen met één methode met openbare [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) preview-methoden, en [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Deze registratiemethoden zijn [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functies en kunnen op elk moment worden gewijzigd. 
> 
>  Met deze methoden worden gegevens geüpload naar uw onderliggende opslag, wat leidt tot opslagkosten. 

## <a name="register-datasets"></a>Gegevenssets registreren

Registreer uw gegevenssets bij een werkruimte om het maakproces te voltooien. Gebruik de methode om gegevenssets te registreren bij uw werkruimte om ze te delen met anderen en ze [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) opnieuw te gebruiken in experimenten in uw werkruimte:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Gegevenssets maken met Azure Resource Manager

Er zijn veel sjablonen [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) op die kunnen worden gebruikt om gegevenssets te maken.

Zie Use an Azure Resource Manager template to create a workspace for Azure Machine Learning (Een Azure Resource Manager gebruiken om een werkruimte te maken [voor Azure Machine Learning.](how-to-create-workspace-template.md)


## <a name="create-datasets-from-azure-open-datasets"></a>Gegevenssets maken van Azure Open Datasets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) zijn samengestelde openbare gegevenssets die u kunt gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Gegevenssets omvatten gegevens uit het openbare domein voor weer, tellingen, vakanties, publieke veiligheid en locaties die u helpen machine learning-modellen te trainen en voorspellende oplossingen te verrijken. Open Datasets zich in de cloud op Microsoft Azure en zijn opgenomen in zowel de SDK als de studio.

Meer informatie over het maken [Azure Machine Learning gegevenssets op basis Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Trainen met gegevenssets

Gebruik uw gegevenssets in uw machine learning voor het trainen van ML-modellen. [Meer informatie over het trainen met gegevenssets](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Versie-gegevenssets

U kunt een nieuwe gegevensset onder dezelfde naam registreren door een nieuwe versie te maken. Een versie van een gegevensset is een manier om een bladwijzer te maken voor de status van uw gegevens, zodat u een specifieke versie van de gegevensset kunt toepassen voor experimenten of toekomstige reproductie. Meer informatie over [gegevenssetversies.](how-to-version-track-datasets.md)
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie over het trainen met gegevenssets](how-to-train-with-datasets.md).
* Gebruik geautomatiseerde machine learning te [trainen met TabularDatasets.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* Zie de voorbeeldnotenotes voor meer voorbeelden van [gegevenssettraining.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)
