---
title: Gegevens wrangling met Apache Spark Pools (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het koppelen en starten van Apache Spark Pools voor gegevens wrangling met Azure Synapse Analytics en Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 9ced4da7f71a0499e538e499644d89240611f1ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956210"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Apache Spark Pools koppelen (aangedreven door Azure Synapse Analytics) voor data wrangling (preview)

In dit artikel leert u hoe u een Apache Spark pool kunt koppelen en starten die is gemaakt door [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) voor data wrangling op schaal. 

Dit artikel bevat richt lijnen voor het interactief uitvoeren van gegevens wrangling binnen een speciale Synapse-sessie in een Jupyter-notebook. Als u liever Azure Machine Learning pijp lijnen gebruikt, raadpleegt u [Apache Spark gebruiken (mogelijk gemaakt door Azure Synapse Analytics) in uw machine learning pijplijn (preview)](how-to-use-synapsesparkstep.md).

>[!IMPORTANT]
> De integratie van Azure Machine Learning en Azure Synapse Analytics is beschikbaar als preview-versie. De mogelijkheden die in dit artikel worden gepresenteerd, gebruiken het `azureml-synapse` pakket met [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functies die op elk gewenst moment kunnen worden gewijzigd.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning en integratie van Azure Synapse Analytics (preview-versie)

Met de integratie van Azure Synapse Analytics met Azure Machine Learning (preview) kunt u een Apache Spark groep koppelen die door Azure Synapse wordt ondersteund voor het verkennen en voorbereiden van interactieve gegevens. Met deze integratie kunt u een specifieke reken kracht voor gegevens wrangling op schaal hebben, allemaal binnen dezelfde python-notebook die u gebruikt voor het trainen van uw machine learning-modellen.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md?tabs=python)

* [Maak een Azure Synapse Analytics-werk ruimte in azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Apache Spark groep maken met behulp van Azure Portal, web tools of Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Configureer uw ontwikkel omgeving](how-to-configure-environment.md) om de Azure machine learning SDK te installeren, of gebruik een [Azure machine learning Compute-exemplaar](concept-compute-instance.md#create) waarbij de SDK al is geïnstalleerd. 

* Installeer het `azureml-synapse` pakket (preview) met de volgende code:

  ```python
  pip install azureml-synapse
  ```

* [Koppeling Azure machine learning werk ruimte en Azure Synapse Analytics-werk ruimte](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Een bestaande gekoppelde service ophalen
Voordat u een toegewezen Compute voor data wrangling kunt koppelen, moet u een ML-werk ruimte hebben die is gekoppeld aan een Azure Synapse Analytics-werk ruimte. dit wordt ook wel een gekoppelde service genoemd. 

Als u een bestaande gekoppelde service wilt ophalen en gebruiken, hebt u **gebruikers-of Inzender** machtigingen nodig voor de Azure Synapse Analytics-werk ruimte.

Bekijk alle gekoppelde services die zijn gekoppeld aan uw machine learning-werk ruimte. 

```python
LinkedService.list(ws)
```

In dit voor beeld wordt een bestaande gekoppelde service opgehaald, `synapselink1` vanuit de werk ruimte, `ws` met de- [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) methode.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark-pool als reken kracht koppelen

Nadat u de gekoppelde service hebt opgehaald, koppelt u een Synapse Apache Spark groep als een toegewezen reken resource voor uw gegevens wrangling-taken. 

U kunt Apache Spark groepen koppelen via,
* Azure Machine Learning Studio
* [ARM-sjablonen (Azure Resource Manager)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* De Python-SDK 

### <a name="attach-a-pool-via-the-studio"></a>Een pool koppelen via de Studio
Volg deze stappen: 

1. Meld u aan bij de [Azure machine learning Studio](https://ml.azure.com/).
1. Selecteer **gekoppelde services** in het gedeelte **beheren** van het linkerdeel venster.
1. Selecteer uw Synapse-werk ruimte.
1. Selecteer in de linkerbovenhoek de **gekoppelde Spark-Pools** . 
1. Selecteer **koppelen**. 
1. Selecteer uw Apache Spark groep in de lijst en geef een naam op.  
    1. Deze lijst bevat de beschik bare Synapse Spark-Pools die aan uw Compute kunnen worden gekoppeld. 
    1. Als u een nieuwe Synapse Spark-pool wilt maken, raadpleegt u [Apache Spark pool maken met de Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Selecteer **geselecteerd koppelen**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Een pool koppelen met de python-SDK

U kunt ook de **python-SDK** gebruiken om een Apache Spark groep te koppelen. 

De volgende code, 
1. Hiermee configureert u de SynapseCompute met,

   1. De LinkedService `linked_service` die u in de vorige stap hebt gemaakt of opgehaald. 
   1. Het type Compute-doel dat u wilt koppelen, `SynapseSpark`
   1. De naam van de Apache Spark groep. Dit moet overeenkomen met een bestaande Apache Spark pool die zich in uw Azure Synapse Analytics-werk ruimte bevindt.
   
1. Hiermee maakt u een machine learning ComputeTarget door door te geven, 
   1. De machine learning-werk ruimte die u wilt gebruiken, `ws`
   1. De naam die u wilt verwijzen naar de compute in de Azure Machine Learning-werk ruimte. 
   1. Het attach_configuration dat u hebt opgegeven bij het configureren van uw Synapse compute.
       1. De aanroep van ComputeTarget. attach () is asynchroon, waardoor de voorbeeld blokken worden geblokkeerd totdat de aanroep is voltooid.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Controleer of de Apache Spark groep is gekoppeld.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Synapse Spark-pool starten voor gegevens voorbereidings taken

Geef de naam van de Apache Spark groep op om te beginnen met de voor bereiding van gegevens met de Apache Spark pool:

> [!IMPORTANT]
> Als u wilt door gaan met het gebruik van de Apache Spark pool, geeft u aan welke reken resource moet worden gebruikt in uw gegevens wrangling met `%synapse` voor één regel code en `%%synapse` voor meerdere regels. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Nadat de sessie is gestart, kunt u de meta gegevens van de sessie controleren.

```python
%synapse meta
```

U kunt een [Azure machine learning omgeving](concept-environments.md) opgeven die tijdens uw Apache Spark sessie moet worden gebruikt. Alleen de Conda-afhankelijkheden die in de omgeving zijn opgegeven, worden van kracht. Docker-installatie kopie wordt niet ondersteund.

>[!WARNING]
>  Python-afhankelijkheden die zijn opgegeven in omgevings Conda-afhankelijkheden worden niet ondersteund in Apache Spark groepen. Op dit moment worden alleen vaste python-versies ondersteund. Controleer uw python-versie door  `sys.version_info` deze op te nemen in uw script.

Met de volgende code wordt de omgeving gemaakt, `myenv` waarmee `azureml-core` versie 1.20.0 en `numpy` versie 1.17.0 worden geïnstalleerd voordat de sessie wordt gestart. U kunt deze omgeving vervolgens toevoegen aan de Apache Spark-sessie- `start` instructie.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Geef de naam van de Apache Spark groep en de omgeving die moet worden gebruikt tijdens de Apache Spark sessie om te beginnen met het voorbereiden van gegevens met de Apache Spark pool en uw aangepaste omgeving. Daarnaast kunt u uw abonnements-ID, de resource groep machine learning werkruimte en de naam van de machine learning-werk ruimte opgeven.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Gegevens uit de opslag laden

Als uw Apache Spark-sessie wordt gestart, leest u de gegevens die u wilt voorbereiden. Het laden van gegevens wordt ondersteund voor Azure Blob Storage en Azure Data Lake Storage generaties 1 en 2.

Er zijn twee manieren om gegevens uit deze opslag services te laden: 

* Gegevens rechtstreeks laden uit de opslag met behulp van het bestand met Hadoop Distributed File System (HDFS).

* Gegevens uit een bestaande [Azure machine learning gegevensset](how-to-create-register-datasets.md)lezen.

Voor toegang tot deze opslag Services hebt u machtigingen voor de **opslag-BLOB-gegevens lezer** nodig. Als u van plan bent om gegevens terug te schrijven naar deze opslag Services, hebt u machtigingen voor toegang tot de **opslag-BLOB-gegevens** nodig. Meer [informatie over opslag machtigingen en-rollen](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Gegevens laden met een HDFS-pad (Hadoop Distributed File System)

Als u gegevens in de opslag ruimte wilt laden en lezen met het corresponderende HDFS-pad, moet u uw referenties voor gegevens toegang direct beschikbaar hebben. Deze referenties variëren afhankelijk van uw opslag type.  

De volgende code laat zien hoe u gegevens kunt lezen van een **Azure Blob-opslag** in een Spark-data frame met uw SAS-token (Shared Access Signature) of toegangs sleutel. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

De volgende code laat zien hoe u gegevens in **Azure data Lake Storage Generation 1 (ADLS gen 1)** kunt lezen met uw referenties voor de Service-Principal. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

De volgende code laat zien hoe u gegevens in **Azure data Lake Storage Generation 2 (ADLS gen 2)** kunt lezen met uw referenties voor de Service-Principal. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Gegevens uit geregistreerde data sets lezen

U kunt ook een bestaande geregistreerde gegevensset in uw werk ruimte ophalen en de gegevens voorbereiding hierop uitvoeren door deze te converteren naar een Spark-data frame.

In het volgende voor beeld wordt de werk ruimte geverifieerd, wordt een geregistreerde TabularDataset, `blob_dset` , die verwijst naar bestanden in Blob Storage, opgehaald en omgezet in een Spark data frame. Wanneer u uw gegevens sets converteert naar een Spark-data frame, kunt u gebruikmaken van bibliotheken voor het `pyspark` verkennen van gegevens en voor bereiding.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Gegevens wrangling taken uitvoeren

Nadat u uw gegevens hebt opgehaald en bekeken, kunt u gegevens wrangling taken uitvoeren.

De volgende code, uitvouwen op het HDFS-voor beeld in de vorige sectie en filtert de gegevens in Spark data frame, `df` op basis van de kolom **Survivor** en groepen die op **leeftijd** worden weer gegeven

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Gegevens opslaan in opslag en Spark-sessie stoppen

Wanneer het verkennen en voorbereiden van uw gegevens is voltooid, moet u uw voor bereide gegevens opslaan voor later gebruik in uw opslag account in Azure.

In het volgende voor beeld worden de voor bereide gegevens teruggeschreven naar de Azure Blob-opslag en wordt het oorspronkelijke `Titanic.csv` bestand in de map overschreven `training_data` . Als u wilt terugschrijven naar opslag, hebt u machtigingen voor de toegang tot de **BLOB-gegevens** van de opslag. Meer [informatie over opslag machtigingen en-rollen](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Wanneer u klaar bent met het voorbereiden van gegevens en het opslaan van uw voor bereide gegevens naar opslag, stopt u met de volgende opdracht om uw Apache Spark pool te gebruiken.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Gegevensset maken om voor bereide gegevens weer te geven

Wanneer u klaar bent om uw voor bereide gegevens voor model trainingen te gebruiken, kunt u verbinding maken met uw opslag met een [Azure machine learning gegevens](how-to-access-data.md)opslag en opgeven welke bestanden u wilt gebruiken met een [Azure machine learning-gegevensset](how-to-create-register-datasets.md).

Het volgende code voorbeeld,

* Er wordt van uitgegaan dat u al een gegevens opslag hebt gemaakt die verbinding maakt met de opslag service waar u de voor bereide gegevens hebt opgeslagen.  
* Hiermee haalt u de bestaande gegevens opslag, `mydatastore` , in de werk ruimte, op `ws` met de methode Get ().
* Hiermee maakt u een [FileDataset](how-to-create-register-datasets.md#filedataset), `train_ds` , die verwijst naar de voor bereide gegevens bestanden die zich in de map bevinden `training_data` in `mydatastore` .  
* Hiermee maakt u de variabele `input1` , die op een later tijdstip kan worden gebruikt om de gegevens bestanden van de `train_ds` gegevensset beschikbaar te maken voor een compute-doel.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>Voorbeeld van notebook

Zie dit [end-to-end-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) voor een gedetailleerde voorbeeld code voor het uitvoeren van gegevens voorbereiding en het model leren van een enkele notebook met Azure Synapse Analytics en Azure machine learning.

## <a name="next-steps"></a>Volgende stappen

* [Train een model](how-to-set-up-training-targets.md).
* [Trainen met Azure Machine Learning-gegevensset](how-to-train-with-datasets.md)
