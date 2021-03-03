---
title: Gegevens voorbereiding met Spark-Pools (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het koppelen van Spark-Pools voor gegevens voorbereiding met Azure Synapse en Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 87e03b6aee122c5a26d4388ca8b570aa6cdf7b55
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662307"
---
# <a name="attach-synapse-spark-pools-for-data-preparation-with-azure-synapse-preview"></a>Synapse Spark-Pools koppelen voor gegevens voorbereiding met Azure Synapse (preview-versie)

In dit artikel leert u hoe u een Apache Spark groep kunt koppelen en openen die door [Azure Synapse](/synapse-analytics/overview-what-is.md) wordt ondersteund voor het voorbereiden van gegevens. 

>[!IMPORTANT]
> De Azure Machine Learning-en Azure Synapse-integratie is beschikbaar als preview-versie. De mogelijkheden die in dit artikel worden gepresenteerd, gebruiken het `azureml-synapse` pakket met [experimentele](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) preview-functies die op elk gewenst moment kunnen worden gewijzigd.

## <a name="azure-machine-learning-and-azure-synapse-integration-preview"></a>Azure Machine Learning-en Azure Synapse-integratie (preview-versie)

Met de Azure Synapse-integratie met Azure Machine Learning (preview) kunt u een Apache Spark groep koppelen die door Azure Synapse wordt ondersteund voor het verkennen en voorbereiden van interactieve gegevens. Met deze integratie kunt u een specifieke Compute voor gegevens voorbereiding op schaal hebben, allemaal binnen dezelfde python-notebook die u gebruikt voor het trainen van uw machine learning modellen.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md?tabs=python)

* [Maak een Synapse-werk ruimte in azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Apache Spark groep maken met behulp van Azure Portal, web tools of Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Installeer de Azure machine learning PYTHON SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) die het `azureml-synapse` pakket bevat (preview). 
    * U kunt het zelf ook zelf installeren, maar dit is alleen compatibel met SDK-versie 1,20 of hoger. 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-assets"></a>Koppeling machine learning werk ruimte en Synapse-assets

Voordat u een Synapse Spark-pool voor gegevens voorbereiding kunt koppelen, moet uw Azure Machine Learning-werk ruimte zijn gekoppeld aan uw Azure Synapse-werk ruimte. 

U kunt uw Synapse-werk ruimte en de werk ruimte van uw ML koppelen via de [python-SDK](#link-sdk) of de [Azure machine learning Studio](#link-studio). 

> [!IMPORTANT]
> Als u een koppeling wilt maken naar de Synapse-werk ruimte, moet u de rol **eigenaar** van de Synapse-werk ruimte krijgen. Controleer uw toegang in de [Azure Portal](https://ms.portal.azure.com/).
>
> Als u geen **eigenaar** bent van de Synapse-werk ruimte, maar een bestaande gekoppelde service wilt gebruiken, raadpleegt u [een bestaande gekoppelde service ophalen](#get-an-existing-linked-service).


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>Werk ruimten koppelen met de python-SDK

De volgende code maakt gebruik van [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) de [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) klassen en en 

* Koppel uw machine learning-werk ruimte aan `ws` uw Azure Synapse-werk ruimte. 
* Registreer uw Synapse-werk ruimte met Azure Machine Learning als een gekoppelde service.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> Er wordt een beheerde identiteit `system_assigned_identity_principal_id` voor elke gekoppelde service gemaakt. Aan deze beheerde identiteit moet de **Synapse Apache Spark** beheerdersrol van de Synapse-werk ruimte worden verleend voordat u uw Synapse-sessie start. [Wijs de Synapse Apache Spark beheerdersrol toe aan de beheerde identiteit in de Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Als u wilt zoeken naar `system_assigned_identity_principal_id` een specifieke gekoppelde service, gebruikt u `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>Werk ruimten koppelen via Studio

Koppel uw machine learning-werk ruimte en de Synapse-werk ruimte via de Azure Machine Learning Studio met de volgende stappen: 

1. Meld u aan bij de [Azure machine learning Studio](https://ml.azure.com/).
1. Selecteer **gekoppelde services** in het gedeelte **beheren** van het linkerdeel venster.
1. Selecteer **integratie toevoegen**.
1. Vul de velden in op het formulier **werk ruimte koppelen**

   |Veld| Beschrijving    
   |---|---
   |Name| Geef een naam op voor de gekoppelde service. Deze naam wordt gebruikt om te verwijzen naar deze specifieke gekoppelde service.
   |Abonnementsnaam | Selecteer de naam van uw abonnement dat is gekoppeld aan uw machine learning-werk ruimte. 
   |Synapse-werk ruimte | Selecteer de Synapse-werk ruimte waarmee u een koppeling wilt maken. 
   
1. Selecteer **volgende** om het formulier **Spark-Pools selecteren (optioneel)** te openen. Op dit formulier selecteert u welke Synapse Spark-pool u aan uw werk ruimte wilt koppelen

1. Selecteer **volgende** om het **controle** formulier te openen en uw selecties te controleren. 
1. Selecteer **maken** om het gekoppelde proces voor het maken van de service te volt ooien.

## <a name="get-an-existing-linked-service"></a>Een bestaande gekoppelde service ophalen

Als u een bestaande gekoppelde service wilt ophalen en gebruiken, moet u machtigingen voor de **gebruiker of Inzender** hebben voor de Synapse-werk ruimte.

In dit voor beeld wordt een bestaande gekoppelde service opgehaald, `synapselink1` vanuit de werk ruimte, `ws` met de- [`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-) methode.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>Gekoppelde services beheren

Als u uw werk ruimten wilt ontkoppelen, gebruikt u de `unregister()` methode

``` python
linked_service.unregister()
```

Bekijk alle gekoppelde services die zijn gekoppeld aan uw machine learning-werk ruimte. 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark-pool als reken kracht koppelen

Als uw werk ruimten zijn gekoppeld, koppelt u een Synapse Spark-pool als een toegewezen reken resource voor uw gegevens voorbereidings taken. 

U kunt Synapse Spark-Pools koppelen via,
* Azure Machine Learning Studio
* [ARM-sjablonen (Azure Resource Manager)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* De Python-SDK 

Volg deze stappen om een Synapse Spark-pool te koppelen met behulp van Studio. 

1. Meld u aan bij de [Azure machine learning Studio](https://ml.azure.com/).
1. Selecteer **gekoppelde services** in het gedeelte **beheren** van het linkerdeel venster.
1. Selecteer uw Synapse-werk ruimte.
1. Selecteer in de linkerbovenhoek de **gekoppelde Spark-Pools** . 
1. Selecteer **koppelen**. 
1. Selecteer uw Synapse Spark-groep in de lijst en geef een naam op.  
    1. Deze lijst bevat de beschik bare Synapse Spark-Pools die aan uw Compute kunnen worden gekoppeld. 
    1. Als u een nieuwe Synapse Spark-pool wilt maken, raadpleegt u [Apache Spark pool maken met de Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Selecteer **geselecteerd koppelen**. 


U kunt ook de **python-SDK** gebruiken om een Synapse Spark-pool te koppelen. 

De volgende code, 
1. Hiermee configureert u de SynapseCompute met,

   1. De LinkedService `linked_service` die u in de vorige stap hebt gemaakt of opgehaald. 
   1. Het type Compute-doel dat u wilt koppelen, `SynapseSpark`
   1. De naam van de Synapse Spark-pool. Dit moet overeenkomen met een bestaande Apache Spark pool die zich in uw Synapse-werk ruimte bevindt.
   
1. Hiermee maakt u een machine learning ComputeTarget door door te geven, 
   1. De machine learning-werk ruimte die u wilt gebruiken, `ws`
   1. De naam die u wilt verwijzen naar de compute in de machine learning-werk ruimte. 
   1. Het attach_configuration dat u hebt opgegeven bij het configureren van uw SynapseCompute.
       1. De aanroep van ComputeTarget. attach () is asynchroon, waardoor de voorbeeld blokken worden geblokkeerd totdat de aanroep is voltooid.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Controleer of de Synapse Spark-pool is gekoppeld.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Synapse Spark-pool starten voor gegevens voorbereidings taken

U kunt een [Azure machine learning omgeving](concept-environments.md) opgeven die tijdens uw Synapse-sessie moet worden gebruikt. Alleen de Conda-afhankelijkheden die in de omgeving zijn opgegeven, worden van kracht. Docker-installatie kopie wordt niet ondersteund.

>[!WARNING]
>  Python-afhankelijkheden die zijn opgegeven in omgevings Conda-afhankelijkheden worden niet ondersteund in Synapse Spark-Pools. Op dit moment worden alleen vaste python-versies ondersteund. Controleer uw python-versie door  `sys.version_info` deze op te nemen in uw script.

Met de volgende code wordt de omgeving gemaakt, `myenv` waarmee `azureml-core` versie 1.20.0 en `numpy` versie 1.17.0 worden geïnstalleerd voordat de sessie wordt gestart. U kunt deze omgeving vervolgens toevoegen aan de Synapse-sessie- `start` instructie.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Als u gegevens voorbereiding met de Synapse Spark-pool wilt starten, geeft u de naam van de Synapse Spark op en geeft u uw abonnements-ID, de machine learning werkruimte resource groep, de naam van de machine learning-werk ruimte en welke omgeving u tijdens de Synapse-sessie wilt gebruiken. 

> [!IMPORTANT]
> Als u wilt door gaan met het gebruik van de Synapse Spark-pool, moet u aangeven welke reken resource moet worden gebruikt in uw gegevens voorbereidings taken met `%synapse` voor één regel met code en `%%synapse` voor meerdere regels. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

Nadat de sessie is gestart, kunt u de meta gegevens van de sessie controleren.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>Gegevens uit de opslag laden

Als uw Synapse Spark-sessie wordt gestart, leest u de gegevens die u wilt voorbereiden. Het laden van gegevens wordt ondersteund voor Azure Blob Storage en Azure Data Lake Storage generaties 1 en 2.

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
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

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

In het volgende voor beeld wordt een geregistreerde TabularDataset, `blob_dset` , die verwijst naar bestanden in Blob Storage, opgehaald en omgezet in een Spark data frame. Wanneer u uw gegevens sets converteert naar een Spark-data frame, kunt u gebruikmaken van bibliotheken voor het `pyspark` verkennen van gegevens en voor bereiding.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-preparation-tasks"></a>Gegevens voorbereidings taken uitvoeren

Nadat u uw gegevens hebt opgehaald en bekeken, kunt u gegevens voorbereidings taken uitvoeren.

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

Wanneer u klaar bent met het voorbereiden van gegevens en het opslaan van uw voor bereide gegevens naar opslag, stopt u met het gebruik van uw Synapse Spark-pool met de volgende opdracht.

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

Zie dit [end-to-end-notebook](../synapse-analytics/overview-what-is.md) voor een gedetailleerde voorbeeld code voor het uitvoeren van gegevens voorbereiding en het model leren van een enkele notebook met Azure Synapse en Azure machine learning.

## <a name="next-steps"></a>Volgende stappen

* [Train een model](how-to-set-up-training-targets.md).
* [Trainen met Azure Machine Learning-gegevensset](how-to-train-with-datasets.md)
* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md).

