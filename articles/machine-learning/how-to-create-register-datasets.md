---
title: Azure Machine Learning-gegevenssets maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van Azure Machine Learning gegevens sets voor het openen van de machine learning-experimenten.
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
ms.openlocfilehash: 81779d942b31f940d579de623ecb39c35d3a8b14
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642137"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning-gegevenssets maken

In dit artikel leert u hoe u Azure Machine Learning gegevens sets maakt om toegang te krijgen tot gegevens voor uw lokale of externe experimenten met de Azure Machine Learning python SDK. Zie het artikel over [beveiligde toegang](concept-data.md#data-workflow) als u wilt weten waar gegevens sets passen in de algehele werk stroom van Azure machine learning Data Access.

Als u een gegevensset maakt, maakt u ook een verwijzing naar de locatie van de gegevensbron, samen met een kopie van de bijbehorende metagegevens. Omdat de gegevens zich op de bestaande locatie blijven, ontstaan er geen extra opslag kosten en wordt de integriteit van uw gegevens bronnen niet bedreigd. Ook gegevens sets worden geëvalueerd in vertraagd, die hulp middelen in de snelheid van werk stroom prestaties. U kunt gegevens sets maken op basis van gegevens opslag, open bare Url's en [Azure open gegevens sets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Maak voor een ervaring met weinig code [Azure machine learning gegevens sets met de Azure machine learning Studio.](how-to-connect-data-ui.md#create-datasets)

Met Azure Machine Learning gegevens sets kunt u het volgende doen:

* Bewaar één kopie van de gegevens in uw opslag, waarnaar wordt verwezen door gegevens sets.

* Naadloos toegang krijgen tot gegevens tijdens model training zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden. Meer [informatie over het trainen van gegevens sets](how-to-train-with-datasets.md).

* Gegevens delen en samen werken met andere gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en gebruiken, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python is geïnstalleerd](/python/api/overview/azure/ml/install), waaronder het pakket met de azureml-gegevens sets.

    * Maak een [Azure machine learning Compute-exemplaar](how-to-create-manage-compute-instance.md), een volledig geconfigureerde en beheerde ontwikkel omgeving met daarin geïntegreerde notebooks en de SDK die al is geïnstalleerd.

    **OF**

    * Werk met uw eigen Jupyter-notebook en installeer de SDK zelf met [deze instructies](/python/api/overview/azure/ml/install).

> [!NOTE]
> Sommige dataset-klassen hebben afhankelijkheden van het pakket voor [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) . Dit is alleen compatibel met 64-bits python. Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) en CentOS (7). Als u niet-ondersteunde distributies gebruikt, volgt u [deze hand leiding](/dotnet/core/install/linux) om .net Core 2,1 te installeren om door te gaan. 

## <a name="compute-size-guidance"></a>Richt lijn voor reken capaciteit

Wanneer u een gegevensset maakt, controleert u de verwerkings kracht van de reken capaciteit en de grootte van uw gegevens in het geheugen. De grootte van uw gegevens in de opslag is niet hetzelfde als de grootte van de gegevens in een data frame. Gegevens in CSV-bestanden kunnen bijvoorbeeld tot 10x worden uitgebreid in een data frame, zodat een CSV-bestand van 1 GB 10 GB kan worden in een data frame. 

Als uw gegevens zijn gecomprimeerd, kunnen ze verder worden uitgebreid. 20 GB aan relatief sparse gegevens die zijn opgeslagen in de gecomprimeerde Parquet-indeling kunnen worden uitgebreid naar ~ 800 GB in het geheugen. Aangezien Parquet-bestanden gegevens in een kolom indeling opslaan en u alleen de helft van de kolommen nodig hebt, hoeft u niet meer dan ~ 400 GB in het geheugen te laden.

Meer [informatie over het optimaliseren van gegevens verwerking in azure machine learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Typen gegevenssets

Er zijn twee typen gegevensset, op basis van de manier waarop gebruikers ze in training gebruiken. FileDatasets en TabularDatasets. Beide typen kunnen worden gebruikt in Azure Machine Learning trainings werk stromen met inschattingen, AutoML, hyperDrive en pijp lijnen. 

### <a name="filedataset"></a>FileDataset

Een [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. Als uw gegevens al zijn gereinigd en klaar zijn om te worden gebruikt in trainings experimenten, kunt u de bestanden [downloaden of koppelen](how-to-train-with-datasets.md#mount-vs-download) aan uw Compute als een FileDataset-object. 

We raden FileDatasets aan voor uw machine learning-werk stromen, aangezien de bron bestanden een wille keurige indeling hebben, waardoor een breder scala van machine learning scenario's mogelijk is, waaronder diep gaande lessen.

Maak een FileDataset met de [python-SDK](#create-a-filedataset) of de [Azure machine learning Studio](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

Een [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) vertegenwoordigt gegevens in tabel vorm door het bestand of de lijst met bestanden te parseren. Dit biedt u de mogelijkheid om de gegevens in een Panda of Spark-data frame te realiseren, zodat u kunt werken met vertrouwde gegevens voorbereiding en trainings bibliotheken zonder dat u uw notebook hoeft te verlaten. U kunt een `TabularDataset` object maken van. CSV-,. tsv-,. Parquet-,. json-bestanden en uit [SQL-query resultaten](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Met TabularDatasets kunt u een tijds tempel opgeven van een kolom in de gegevens of van de locatie waar de patroon gegevens voor het pad zijn opgeslagen om een time series-eigenschappen in te scha kelen. Met deze specificatie kunt u eenvoudig en efficiënt filteren op tijd. Zie voor een voor beeld [Time Series-gerelateerde API-demo met NOAA-weer gegevens](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Maak een TabularDataset met [de python-SDK](#create-a-tabulardataset) of [Azure machine learning Studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> [Automated ml](concept-automated-ml.md) werk stromen die zijn gegenereerd via de Azure machine learning Studio ondersteunen momenteel alleen TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Toegang tot gegevens sets in een virtueel netwerk

Als uw werk ruimte zich in een virtueel netwerk bevindt, moet u de gegevensset configureren om validatie over te slaan. Zie [een werk ruimte en gekoppelde resources beveiligen](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)voor meer informatie over het gebruik van data stores en gegevens sets in een virtueel netwerk.

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Gegevens sets maken op basis van gegevens opslag

Gegevens sets moeten worden gemaakt op basis van paden in [Azure machine learning data stores](how-to-access-data.md) of Web-url's, zodat deze door Azure machine learning toegankelijk zijn. 

> [!TIP] 
> U kunt rechtstreeks gegevens sets maken op basis van opslag-url's die toegang hebben tot de identiteit. Meer informatie over [verbinding maken met opslag met op identiteit gebaseerde gegevens toegang (preview)](how-to-identity-based-data-access.md)<br><br>
Deze mogelijkheid is een [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functie en kan op elk gewenst moment worden gewijzigd. 

 
Gegevens sets maken op basis van een gegevens opslag met de python-SDK:

1. Controleer of u toegang hebt tot `contributor` `owner` de onderliggende opslag service van uw geregistreerde Azure machine learning Data Store. [Controleer de machtigingen van uw opslag account in de Azure Portal](../role-based-access-control/check-access.md).

1. Maak de gegevensset door te verwijzen naar de paden in het gegevens archief. U kunt een gegevensset maken op basis van meerdere paden in meerdere gegevens opslag. Er is geen vaste limiet voor het aantal bestanden of gegevens grootte waaruit u een gegevensset kunt maken. 

> [!NOTE]
> Voor elk gegevenspad worden enkele aanvragen verzonden naar de opslag service om te controleren of deze naar een bestand of map verwijst. Deze overhead kan leiden tot slechtere prestaties of storingen. Een gegevensset die verwijst naar één map met 1000 bestanden in, wordt beschouwd als verwijzingen naar één gegevenspad. U kunt het beste een gegevensset maken die verwijst naar minder dan 100 paden in gegevens opslag voor optimale prestaties.

### <a name="create-a-filedataset"></a>Een FileDataset maken

Gebruik de [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) methode in de- `FileDatasetFactory` klasse om bestanden in een wille keurige indeling te laden en een niet-geregistreerde FileDataset te maken. 

Als uw opslag zich achter een virtueel netwerk of een firewall bevindt, stelt u de para meter `validate=False` in uw `from_files()` methode in. Hiermee wordt de eerste validatie stap omzeild en zorgt u ervoor dat u uw gegevensset kunt maken op basis van deze beveiligde bestanden. Meer informatie over het [gebruik van data stores en gegevens sets in een virtueel netwerk](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Als u gegevens sets in het experiment opnieuw wilt gebruiken en delen in uw werk ruimte, moet [u uw gegevensset registreren](#register-datasets). 

> [!TIP] 
> Upload bestanden uit een lokale map en maak een FileDataset in één methode met de open bare preview-methode, [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Deze methode is een [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functie en kan op elk gewenst moment worden gewijzigd. 
> 
>  Met deze methode worden gegevens naar uw onderliggende opslag geüpload, waardoor opslag kosten in rekening worden gebracht. 

### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

Gebruik de [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) methode in de `TabularDatasetFactory` klasse om bestanden te lezen in de CSV-of. TSV-indeling en om een niet-geregistreerde TabularDataset te maken. Als u een lees bewerking uitvoert van meerdere bestanden, worden de resultaten samengevoegd in één tabel weergave. 

Als uw opslag zich achter een virtueel netwerk of een firewall bevindt, stelt u de para meter `validate=False` in uw `from_delimited_files()` methode in. Hiermee wordt de eerste validatie stap omzeild en zorgt u ervoor dat u uw gegevensset kunt maken op basis van deze beveiligde bestanden. Meer informatie over het gebruik [van data stores en gegevens sets in een virtueel netwerk](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

Met de volgende code worden de bestaande werk ruimte en de gewenste gegevens opslag op naam opgehaald. En vervolgens worden de gegevens opslag en bestands locaties door gegeven aan de `path` para meter om een nieuwe TabularDataset te maken `weather_ds` .

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
### <a name="set-data-schema"></a>Gegevens schema instellen

Wanneer u een TabularDataset maakt, worden de kolom gegevens typen standaard automatisch afgeleid. Als de instelde typen niet overeenkomen met uw verwachtingen, kunt u uw gegevensset-schema bijwerken door kolom typen met de volgende code op te geven. De para meter `infer_column_type` is alleen van toepassing op gegevens sets die zijn gemaakt van bestanden met scheidings tekens. Meer [informatie over ondersteunde gegevens typen](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|TabIndex|PassengerId|Dummy tekst|Pclass|Name|Seks|Leeftijd|SibSp|Parch|Ticket|Tickets|Hand|Ingeschepend
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Niet waar|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|Waar|1|Cumings, Mevr. John Bradley (Florence Briggs th...|vrouwelijk|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Waar|3|Heikkinen, missen. Laina|vrouwelijk|26.0|0|0|STON/O2. 3101282|7,9250||S

Als u gegevens sets wilt hergebruiken en delen in experimenten in uw werk ruimte, moet [u uw gegevensset registreren](#register-datasets).

## <a name="wrangle-data"></a>Wrangle-gegevens
Nadat u uw gegevensset hebt gemaakt en [geregistreerd](#register-datasets) , kunt u deze in uw notebook laden voor gegevens wrangling en [onderzoek](#explore-data) voordat u een model training maakt. 

Als u geen gegevens wrangling of onderzoek nodig hebt, raadpleegt u gegevens sets in uw trainings scripts gebruiken voor het indienen van ML-experimenten in [trein met gegevens sets](how-to-train-with-datasets.md).

### <a name="filter-datasets-preview"></a>Gegevens sets filteren (preview-versie)
Filter mogelijkheden zijn afhankelijk van het type gegevensset dat u hebt. 
> [!IMPORTANT]
> Het filteren van gegevens sets met de open bare preview-methode [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) is een [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functie en kan op elk gewenst moment worden gewijzigd. 
> 
**Voor TabularDatasets** kunt u kolommen met de methoden [keep_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) en [drop_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) bijeenhouden of verwijderen.

Als u rijen wilt filteren op een bepaalde kolom waarde in een TabularDataset, gebruikt u de methode [filter ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (preview). 

In de volgende voor beelden wordt een niet-geregistreerde gegevensset geretourneerd op basis van de opgegeven expressies.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**In FileDatasets** correspondeert elke rij met een pad van een bestand, dus filteren op kolom waarde is niet nuttig. U kunt echter wel [() rijen filteren](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) op meta gegevens zoals CreationTime, size, enzovoort.

In de volgende voor beelden wordt een niet-geregistreerde gegevensset geretourneerd op basis van de opgegeven expressies.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

Een speciaal geval is een **gelabelde gegevens sets** die zijn gemaakt op basis van [gegevens label projecten](how-to-create-labeling-projects.md) . Deze gegevens sets zijn een type TabularDataset dat bestaat uit afbeeldings bestanden. Voor deze typen gegevens sets kunt u afbeeldingen [filteren ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) op meta gegevens en op kolom waarden zoals `label` en `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

## <a name="explore-data"></a>Gegevens verkennen

Wanneer u klaar bent met het wrangling van uw gegevens, kunt u uw gegevensset [registreren](#register-datasets) en deze vervolgens laden in uw notebook voor het verkennen van gegevens voorafgaand aan de model training.

Voor FileDatasets kunt u uw gegevensset **koppelen** of **downloaden** en de python-bibliotheken Toep assen die u normaal gesp roken gebruikt voor het verkennen van gegevens. Meer [informatie over koppeling en down load](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Gebruik voor TabularDatasets de [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) methode om uw gegevens in een data frame weer te geven. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|TabIndex|PassengerId|Dummy tekst|Pclass|Name|Seks|Leeftijd|SibSp|Parch|Ticket|Tickets|Hand|Ingeschepend
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Niet waar|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|Waar|1|Cumings, Mevr. John Bradley (Florence Briggs th...|vrouwelijk|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Waar|3|Heikkinen, missen. Laina|vrouwelijk|26.0|0|0|STON/O2. 3101282|7,9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Een gegevensset maken op basis van Pandas data frame

Als u een TabularDataset wilt maken van een in geheugen Panda data frame, schrijft u de gegevens naar een lokaal bestand, zoals een CSV, en maakt u uw gegevensset vanuit dat bestand. De volgende code toont deze werk stroom.

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
> Maak en Registreer een TabularDataset van een in het geheugen Spark-of Panda-data frame met één methode met open bare preview-methoden, [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) en [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Deze registratie methoden zijn [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functies en kunnen op elk gewenst moment worden gewijzigd. 
> 
>  Met deze methoden worden gegevens naar uw onderliggende opslag geüpload, waardoor opslag kosten in rekening worden gebracht. 

## <a name="register-datasets"></a>Gegevens sets registreren

Als u het aanmaak proces wilt volt ooien, registreert u uw gegevens sets met een werk ruimte. Gebruik de [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) methode voor het registreren van gegevens sets met uw werk ruimte om ze te delen met anderen en ze opnieuw te gebruiken voor experimenten in uw werk ruimte:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Gegevens sets maken met behulp van Azure Resource Manager

Er zijn veel sjablonen op [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) die kunnen worden gebruikt om gegevens sets te maken.

Zie [een Azure Resource Manager sjabloon gebruiken om een werk ruimte voor Azure machine learning te maken](how-to-create-workspace-template.md)voor meer informatie over het gebruik van deze sjablonen.


## <a name="create-datasets-from-azure-open-datasets"></a>Gegevens sets maken op basis van Azure open gegevens sets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) zijn samengestelde openbare gegevenssets die u kunt gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Gegevenssets omvatten gegevens uit het openbare domein voor weer, tellingen, vakanties, publieke veiligheid en locaties die u helpen machine learning-modellen te trainen en voorspellende oplossingen te verrijken. Open gegevens sets bevinden zich in de Cloud op Microsoft Azure en zijn opgenomen in zowel de SDK als de Studio.

Meer informatie over het maken [van Azure machine learning gegevens sets van Azure open gegevens sets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Trainen met gegevenssets

Gebruik uw gegevens sets in uw machine learning experimenten voor trainings ML-modellen. Meer [informatie over het trainen van gegevens sets](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Versie gegevens sets

U kunt een nieuwe gegevensset onder dezelfde naam registreren door een nieuwe versie te maken. Een gegevensset-versie is een manier om de status van uw gegevens te bookmarkren, zodat u een specifieke versie van de gegevensset kunt Toep assen voor experimenten of toekomstige reproductie. Meer informatie over de versies van de [gegevensset](how-to-version-track-datasets.md).
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

* Meer informatie [over het trainen van gegevens sets](how-to-train-with-datasets.md).
* Gebruik automatische machine learning om [met TabularDatasets te trainen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Zie de voor beelden van [notitie blokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)voor meer informatie over het trainen van gegevensset.
