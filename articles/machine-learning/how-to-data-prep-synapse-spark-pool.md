---
title: Data-wrangling met Apache Spark pools (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het koppelen en starten van Apache Spark voor data-wrangling met Azure Synapse Analytics en Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 1852bfdb4bf8513aaa5d43993e2b6ff804808dbd
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575646"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Apache Spark groepen (powered by Azure Synapse Analytics) koppelen voor data-wrangling (preview)

In dit artikel leert u hoe u een Apache Spark-pool [powered by Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) aan uw Azure Machine Learning-werkruimte koppelt, zodat u deze kunt starten en gegevens kunt wrangling uitvoeren op schaal. 

Dit artikel bevat richtlijnen voor het interactief uitvoeren van data-wrangling-taken binnen een specifieke Synapse-sessie in een Jupyter-notebook met behulp van [de Azure Machine Learning Python SDK.](/python/api/overview/azure/ml/) Als u liever Azure Machine Learning pijplijnen gebruikt, zie Apache Spark [(powered by Azure Synapse Analytics) gebruiken in uw machine learning-pijplijn (preview).](how-to-use-synapsesparkstep.md)

Als u hulp nodig hebt bij het gebruik van Azure Synapse Analytics synapse-werkruimte, bekijkt u de reeks Azure Synapse Analytics [aan de slag.](../synapse-analytics/get-started.md)

>[!IMPORTANT]
> De Azure Machine Learning en Azure Synapse Analytics-integratie is in preview. De mogelijkheden die in dit artikel worden gepresenteerd, maken gebruik van het `azureml-synapse` pakket dat experimentele preview-functies bevat die op elk moment kunnen [](/python/api/overview/azure/ml/#stable-vs-experimental) veranderen.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning en Azure Synapse Analytics integratie (preview)

Met Azure Synapse Analytics integratie met Azure Machine Learning (preview) kunt u een Apache Spark-pool koppelen die wordt Azure Synapse voor interactieve gegevensverkenning en -voorbereiding. Met deze integratie kunt u een toegewezen rekenproces voor data-wrangling op schaal hebben, allemaal binnen hetzelfde Python-notebook dat u gebruikt voor het trainen van uw machine learning modellen.

## <a name="prerequisites"></a>Vereisten

* De [Azure Machine Learning Python SDK geïnstalleerd.](/python/api/overview/azure/ml/install) 

* [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md?tabs=python)

* [Maak een Azure Synapse Analytics werkruimte in Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Een Apache Spark maken met Azure Portal, webhulpprogramma's of Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Configureer uw ontwikkelomgeving om](how-to-configure-environment.md) de Azure Machine Learning-SDK te installeren of gebruik een [Azure Machine Learning reken-exemplaar](concept-compute-instance.md#create) waarin de SDK al is geïnstalleerd. 

* Installeer het `azureml-synapse` pakket (preview) met de volgende code:

  ```python
  pip install azureml-synapse
  ```

* [Koppel Azure Machine Learning werkruimte aan Azure Synapse Analytics werkruimte](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Een bestaande gekoppelde service op te halen
Voordat u een toegewezen rekenkracht voor data-wrangling kunt koppelen, moet u een ML-werkruimte hebben die is gekoppeld aan een Azure Synapse Analytics-werkruimte. Dit wordt een gekoppelde service genoemd. 

Als u een bestaande gekoppelde service wilt ophalen en gebruiken, hebt **u gebruikers-** of inzendermachtigingen nodig voor de Azure Synapse Analytics werkruimte.

Alles weergeven gekoppelde services die zijn gekoppeld aan uw machine learning werkruimte. 

```python
from azureml.core import LinkedService

LinkedService.list(ws)
```

In dit voorbeeld wordt een bestaande gekoppelde service, , opgehaald uit `synapselink1` de werkruimte, `ws` , met de [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) methode .
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark-pool koppelen als rekenkracht

Zodra u de gekoppelde service hebt opgehaald, koppelt u een Synapse Apache Spark-pool als een toegewezen rekenresource voor uw data-wrangling-taken. 

U kunt Apache Spark-pools koppelen via,
* Azure Machine Learning Studio
* [ARM-sjablonen (Azure Resource Manager)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* De Azure Machine Learning Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Een pool koppelen via de studio
Volg deze stappen: 

1. Meld u aan bij [de Azure Machine Learning-studio](https://ml.azure.com/).
1. Selecteer **Gekoppelde services** in **de sectie** Beheren van het linkerdeelvenster.
1. Selecteer uw Synapse-werkruimte.
1. Selecteer **Linksboven Gekoppelde Spark-pools.** 
1. Selecteer **Koppelen.** 
1. Selecteer uw Apache Spark in de lijst en geef een naam op.  
    1. Deze lijst identificeert de beschikbare Synapse Spark-pools die aan uw rekenkracht kunnen worden gekoppeld. 
    1. Zie Create Apache Spark pool with the Synapse Studio (Een nieuwe Synapse [Spark-pool](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) maken met de Synapse Studio
1. Selecteer **Bij koppelen geselecteerd.** 

### <a name="attach-a-pool-with-the-python-sdk"></a>Een pool koppelen met de Python-SDK

U kunt ook de **Python-SDK gebruiken om** een Apache Spark koppelen. 

De volgende code: 
1. Hiermee configureert u [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) de met,

   1. De [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice) , die u in de vorige stap hebt gemaakt of `linked_service` opgehaald. 
   1. Het type rekendoel dat u wilt koppelen, `SynapseSpark`
   1. De naam van de Apache Spark groep. Dit moet overeenkomen met een bestaande Apache Spark die zich in uw Azure Synapse Analytics werkruimte.
   
1. Hiermee maakt u machine learning [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) door door te geven in, 
   1. De machine learning die u wilt gebruiken, `ws`
   1. De naam die u wilt verwijzen naar de berekening in de Azure Machine Learning werkruimte. 
   1. De attach_configuration u hebt opgegeven bij het configureren van uw Synapse Compute.
       1. De aanroep van ComputeTarget.attach() is asynchroon, dus de voorbeeld-blokken totdat de aanroep is voltooid.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Controleer of Apache Spark pool is gekoppeld.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>Synapse Spark-pool starten voor data-wrangling-taken

Als u de gegevensvoorbereiding wilt starten met Apache Spark pool, geeft u de naam Apache Spark pool op:

> [!IMPORTANT]
> Als u het gebruik van de Apache Spark-pool wilt voortzetten, moet u aangeven welke rekenresource moet worden gebruikt in uw data-wrangling-taken met voor één coderegels en voor `%synapse` `%%synapse` meerdere regels. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Nadat de sessie is gestart, kunt u de metagegevens van de sessie controleren.

```python
%synapse meta
```

U kunt een Azure Machine Learning [opgeven voor](concept-environments.md) gebruik tijdens uw Apache Spark sessie. Alleen Conda-afhankelijkheden die zijn opgegeven in de omgeving, worden van kracht. Docker-afbeelding wordt niet ondersteund.

>[!WARNING]
>  Python-afhankelijkheden die zijn opgegeven in de omgeving Conda-afhankelijkheden worden niet ondersteund in Apache Spark groepen. Momenteel worden alleen vaste Python-versies ondersteund. Controleer uw Python-versie door op te  `sys.version_info` neem in uw script op.

Met de volgende code maakt u de omgeving , , waarmee versie `myenv` `azureml-core` 1.20.0 en `numpy` versie 1.17.0 worden geïnstalleerd voordat de sessie begint. U kunt deze omgeving vervolgens opnemen in uw Apache Spark `start` sessie-instructie.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Als u de gegevensvoorbereiding wilt starten met de Apache Spark-pool en uw aangepaste omgeving, geeft u de naam van de Apache Spark-pool op en welke omgeving u tijdens de Apache Spark gebruiken. Daarnaast kunt u uw abonnements-id, de resourcegroep machine learning werkruimte en de naam van de machine learning werkruimte.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Gegevens laden uit opslag

Zodra de Apache Spark gestart, leest u de gegevens in die u wilt voorbereiden. Het laden van gegevens wordt ondersteund voor Azure Blob Storage en Azure Data Lake Storage Generations 1 en 2.

Er zijn twee manieren om gegevens uit deze opslagservices te laden: 

* Gegevens rechtstreeks uit de opslag laden met behulp van het pad naar Hadoop Distributed Files System (HDFS).

* Gegevens uit een bestaande gegevensset [Azure Machine Learning lezen.](how-to-create-register-datasets.md)

Voor toegang tot deze opslagservices hebt u **machtigingen voor Lezer van opslagblobgegevens** nodig. Als u van plan bent om gegevens terug te schrijven naar deze opslagservices, hebt u de machtiging Bijdrager voor **opslagblobgegevens** nodig. [Meer informatie over opslagmachtigingen en -rollen.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Gegevens laden met HDFS-pad (Hadoop Distributed Files System)

Als u gegevens wilt laden en lezen uit de opslag met het bijbehorende HDFS-pad, moet u de verificatiereferenties voor gegevenstoegang direct beschikbaar hebben. Deze referenties verschillen afhankelijk van uw opslagtype.  

De volgende code laat zien hoe u gegevens uit **een Azure Blob-opslag** kunt lezen in een Spark-gegevensframe met uw SAS-token (Shared Access Signature) of toegangssleutel. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

De volgende code laat zien hoe u gegevens uit **Azure Data Lake Storage Generation 1 (ADLS Gen 1)** kunt lezen met uw referenties voor de service-principal. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

De volgende code laat zien hoe u gegevens uit **Azure Data Lake Storage Generation 2 (ADLS Gen 2) kunt** lezen met uw referenties voor de service-principal. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Gegevens uit geregistreerde gegevenssets inlezen

U kunt ook een bestaande geregistreerde gegevensset in uw werkruimte krijgen en er gegevensvoorbereiding op uitvoeren door deze te converteren naar een Spark-gegevensframe.

In het volgende voorbeeld wordt geverifieerd bij de werkruimte, wordt een geregistreerde TabularDataset, , die verwijst naar bestanden in blobopslag, opgevraagd en ge converteerd naar een `blob_dset` Spark-gegevensframe. Wanneer u uw gegevenssets converteert naar een Spark-gegevensframe, kunt u gebruikmaken van bibliotheken voor gegevensverkenning `pyspark` en -voorbereiding.  

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

## <a name="perform-data-wrangling-tasks"></a>Data-wrangling-taken uitvoeren

Nadat u uw gegevens hebt opgehaald en verkend, kunt u data-wrangling-taken uitvoeren.

De volgende code is een uitbreiding op het HDFS-voorbeeld in de vorige sectie en filtert de gegevens in spark dataframe, , op basis van de kolom Kolom en groepen die worden vermeld op `df` **Leeftijd** 

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Gegevens opslaan in opslag en Spark-sessie stoppen

Zodra het verkennen en voorbereiden van uw gegevens is voltooid, kunt u uw voorbereide gegevens opslaan voor later gebruik in uw opslagaccount in Azure.

In het volgende voorbeeld worden de voorbereide gegevens teruggeschreven naar Azure Blob Storage en wordt het oorspronkelijke bestand `Titanic.csv` in de map `training_data` overschreven. Als u wilt terugschrijven naar de opslag, hebt u **machtigingen voor Bijdrager voor opslagblobgegevens** nodig. [Meer informatie over opslagmachtigingen en -rollen.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Wanneer u de gegevensvoorbereiding hebt voltooid en de voorbereide gegevens hebt opgeslagen in de opslag, stopt u met het gebruik van Apache Spark pool met de volgende opdracht.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Gegevensset maken om voorbereide gegevens weer te geven

Wanneer u klaar bent om uw voorbereide gegevens te gebruiken voor modeltraining, maakt u verbinding met uw opslag met een [Azure Machine Learning-gegevensopslag](how-to-access-data.md)en geeft u op welke bestanden u wilt gebruiken met een [Azure Machine Learning-gegevensset](how-to-create-register-datasets.md).

Het volgende codevoorbeeld:

* Ervan uit dat u al een gegevensopslag hebt gemaakt die verbinding maakt met de opslagservice waar u uw voorbereide gegevens hebt opgeslagen.  
* Haalt dat bestaande gegevensstore, `mydatastore` , op uit de werkruimte, met de methode `ws` get().
* Hiermee maakt [u een FileDataset](how-to-create-register-datasets.md#filedataset), , die verwijst naar de `train_ds` voorbereide gegevensbestanden in de `training_data` map in `mydatastore` .  
* Hiermee maakt u de variabele , die op een later tijdstip kan worden gebruikt om de gegevensbestanden van de `input1` `train_ds` gegevensset beschikbaar te maken voor een rekendoel.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```
## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>Een gebruiken `ScriptRunConfig` om een experiment uit te voeren naar een Synapse Spark-pool

U kunt ook [gebruikmaken van het Synapse Spark-cluster](#attach-a-pool-with-the-python-sdk) dat u eerder hebt gekoppeld als rekendoel voor het verzenden van een experimentuit voer met een [ScriptRunConfig-object.](/python/api/azureml-core/azureml.core.scriptrunconfig)

```Python
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--tabular_input", input1, 
                                                 "--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

Zodra het `ScriptRunConfig` object is ingesteld, kunt u de run verzenden.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```
Zie het voorbeeldnoteboek voor meer informatie, zoals het script dat `dataprep.py` in dit voorbeeld wordt [gebruikt.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb)

## <a name="example-notebooks"></a>Voorbeeldnotebooks

Zie dit [voorbeeldnote notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb) voor meer concepten en demonstraties van de Azure Synapse Analytics en Azure Machine Learning integratiemogelijkheden.

## <a name="next-steps"></a>Volgende stappen

* [Een model trainen.](how-to-set-up-training-targets.md)
* [Trainen met Azure Machine Learning gegevensset](how-to-train-with-datasets.md)
