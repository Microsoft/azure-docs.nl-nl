---
title: Gebruik Apache Spark in een machine learning-pijplijn (preview)
titleSuffix: Azure Machine Learning
description: Koppel uw Azure Synapse Analytics aan uw Azure machine learning-pijplijn om uw Apache Spark te gebruiken voor gegevensmanipulatie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 2c69ec853cdeeed6f9e28fb9f2884053580ce08e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576377"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Een Apache Spark (powered by Azure Synapse Analytics) gebruiken in uw machine learning pijplijn (preview)

In dit artikel leert u hoe u Apache Spark pools powered by Azure Synapse Analytics gebruikt als rekendoel voor een gegevensvoorbereidingsstap in een Azure Machine Learning pijplijn. U leert hoe een enkele pijplijn rekenresources kan gebruiken die geschikt zijn voor de specifieke stap, zoals gegevensvoorbereiding of training. U ziet hoe gegevens worden voorbereid voor de Spark-stap en hoe deze worden doorgegeven aan de volgende stap. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure Machine Learning werkruimte voor](how-to-manage-workspace.md) al uw pijplijnbronnen.

* [Configureer uw ontwikkelomgeving](how-to-configure-environment.md) om de Azure Machine Learning SDK te installeren of gebruik een [Azure Machine Learning compute-exemplaar](concept-compute-instance.md) waarin de SDK al is geïnstalleerd.

* Maak een Azure Synapse Analytics en Apache Spark pool (zie [Quickstart: Een serverloze](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)Apache Spark maken met behulp van Synapse Studio ). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Uw werkruimte Azure Machine Learning koppelen aan Azure Synapse Analytics werkruimte 

U maakt en beheert uw Apache Spark pools in een Azure Synapse Analytics werkruimte. Als u een Apache Spark wilt integreren met Azure Machine Learning werkruimte, moet u een koppeling maken [naar Azure Synapse Analytics werkruimte](how-to-link-synapse-ml-workspaces.md). 

U kunt een groep Apache Spark koppelen via Azure Machine Learning-studio gebruikersinterface met behulp van de **pagina Gekoppelde** services. U kunt dit ook doen via de **pagina Compute** met de optie **Compute koppelen.**

U kunt ook een Apache Spark-pool koppelen via SDK (zoals hieronder wordt beschreven) of via een ARM-sjabloon (zie dit voorbeeld van [een ARM-sjabloon).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json) 

U kunt de opdrachtregel gebruiken om de ARM-sjabloon te volgen, de gekoppelde service toe te voegen en de Apache Spark pool te koppelen met de volgende code:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Als u een koppeling naar Azure Synapse Analytics werkruimte wilt maken, moet u de rol Eigenaar hebben in Azure Synapse Analytics werkruimteresource. Controleer uw toegang in de Azure Portal.
> De gekoppelde service krijgt een door het systeem toegewezen identiteit (SAI) wanneer u deze maakt. U moet deze koppelingsservice SAI de rol 'Synapse Apache Spark administrator' toewijzen vanuit Synapse Studio, zodat deze de Spark-taak kan verzenden (zie [Synapse](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)RBAC-roltoewijzingen beheren in Synapse Studio ). U moet de gebruiker van de werkruimte Azure Machine Learning de rol 'Inzender' van Azure Portal van resourcebeheer.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>De koppeling maken of ophalen tussen uw Azure Synapse Analytics werkruimte en uw Azure Machine Learning werkruimte

U kunt gekoppelde services in uw werkruimte ophalen met code zoals:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Ga eerst naar uw Azure Machine Learning werkruimte met behulp van de configuratie in (zie Zelfstudie: Aan de slag `Workspace.from_config()` met Azure Machine Learning in uw `config.json` [ontwikkelomgeving).](tutorial-1st-experiment-sdk-setup-local.md) Vervolgens worden met de code alle gekoppelde services afgedrukt die beschikbaar zijn in de werkruimte. Tot slot `LinkedService.get()` wordt een gekoppelde service met de naam `'synapselink1'` opgehaald. 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Uw Apache Spark-pool koppelen als een rekendoel voor Azure Machine Learning

Als u uw Apache Spark-pool wilt gebruiken om een stap in uw machine learning-pijplijn aan te zetten, moet u deze koppelen als een voor de pijplijnstap, zoals wordt weergegeven `ComputeTarget` in de volgende code.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

De eerste stap is het configureren van `SynapseCompute` de . Het argument is het object dat u in de vorige stap hebt `linked_service` `LinkedService` gemaakt of opgehaald. Het `type` argument moet `SynapseSpark` zijn. Het `pool_name` argument in moet overeenkomen met dat van een bestaande pool in Azure Synapse Analytics `SynapseCompute.attach_configuration()` werkruimte. Zie [Quickstart: Een serverloze Apache Spark-pool](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)maken met behulp van Synapse Studio voor meer informatie over het maken van een Apache Spark-pool in de Azure Synapse Analytics-werkruimte. Het type `attach_config` is `ComputeTargetAttachConfiguration` .

Zodra de configuratie is gemaakt, maakt u een machine learning door de , en de naam door te geven waarmee u wilt verwijzen naar de berekening in de `ComputeTarget` `Workspace` machine learning `ComputeTargetAttachConfiguration` werkruimte. De aanroep `ComputeTarget.attach()` van is asynchroon, dus de voorbeeldblokken totdat de aanroep is voltooid.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Een maken `SynapseSparkStep` die gebruikmaakt van de Apache Spark pool

De voorbeeldnotenotenote [Spark-taak in een Apache Spark-pool](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) definieert een eenvoudige machine learning pijplijn. Eerst definieert het notebook een gegevensvoorbereidingsstap powered by `synapse_compute` de die in de vorige stap is gedefinieerd. Vervolgens definieert het notebook een trainingsstap powered by een rekendoel dat beter geschikt is voor training. Het voorbeeldnote notebook maakt gebruik van de Titanic-survivaldatabase om gegevensinvoer en -uitvoer te demonstreren; De gegevens worden niet daadwerkelijk opschoont of een voorspellend model wordt gemaakt. Omdat dit voorbeeld geen echte training heeft, maakt de trainingsstap gebruik van een goedkope rekenresource op basis van CPU.

Gegevens stromen naar een machine learning pijplijn via objecten, die tabellaire gegevens `DatasetConsumptionConfig` of sets bestanden kunnen bevatten. De gegevens zijn vaak afkomstig uit bestanden in blobopslag in de gegevensopslag van een werkruimte. De volgende code toont een aantal typische code voor het maken van invoer voor een machine learning pijplijn:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

In de bovenstaande code wordt ervan uitgenomen dat het bestand `Titanic.csv` zich in de blobopslag. De code laat zien hoe u het bestand leest als een `TabularDataset` en als een `FileDataset` . Deze code is alleen bedoeld voor demonstratiedoeleinden, omdat het verwarrend zou zijn om invoer te dupliceren of om één gegevensbron te interpreteren als een tabel met resources en alleen als een bestand.

> [!Important]
> Als u een als `FileDataset` invoer wilt gebruiken, moet `azureml-core` uw versie ten minste `1.20.0` zijn. Hieronder wordt beschreven hoe u dit `Environment` opgeeft met behulp van de klasse .

Wanneer een stap is voltooid, kunt u ervoor kiezen om uitvoergegevens op te slaan met behulp van code die vergelijkbaar is met:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

In dit geval worden de gegevens opgeslagen in de in een bestand met de naam en zijn ze beschikbaar in de machine learning werkruimte als een met `datastore` `test` de naam `Dataset` `registered_dataset` .

Naast gegevens kan een pijplijnstap afhankelijkheden per stap van Python hebben. Afzonderlijke `SynapseSparkStep` objecten kunnen ook hun precieze Azure Synapse Apache Spark opgeven. Dit wordt weergegeven in de volgende code, waarmee wordt aangegeven dat de pakketversie ten `azureml-core` minste moet `1.20.0` zijn. (Zoals eerder vermeld, is deze vereiste `azureml-core` voor nodig om een als invoer te `FileDataset` gebruiken.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

Met de bovenstaande code wordt één stap in de Azure machine learning opgegeven. Met deze stap geeft u een specifieke versie op en kunt u indien nodig andere `environment` `azureml-core` conda- of pip-afhankelijkheden toevoegen.

De `SynapseSparkStep` zipt en uploadt vanaf de lokale computer de subdirectory `./code` . Deze map wordt opnieuw gemaakt op de rekenserver en met de stap wordt het bestand vanuit die `dataprep.py` map uitgevoerd. De `inputs` en van die stap zijn de objecten , en die eerder zijn `outputs` `step1_input1` `step1_input2` `step1_output` besproken. De eenvoudigste manier om toegang te krijgen tot deze waarden in het `dataprep.py` script is door ze te koppelen aan met de naam `arguments` .

De volgende set argumenten voor het `SynapseSparkStep` constructorbesturingselement Apache Spark. De `compute_target` is de die we eerder als `'link1-spark01'` rekendoel hebben gekoppeld. De andere parameters geven het geheugen en de kernen op die we willen gebruiken.

In het voorbeeldnote notebook wordt de volgende code gebruikt voor `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Met dit script voor gegevensvoorbereiding wordt geen echte gegevenstransformatie gedaan, maar wordt geïllustreerd hoe u gegevens op haalt, converteert naar een Spark-gegevensframe en hoe u eenvoudige bewerkingen Apache Spark uitvoeren. U vindt de uitvoer in Azure Machine Learning Studio door de onderliggende run te openen, het tabblad **Uitvoer en** logboeken te kiezen en het bestand te openen, zoals wordt weergegeven in de `logs/azureml/driver/stdout` volgende afbeelding.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Schermopname van Studio met het tabblad stdout van onderliggende run":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>De `SynapseSparkStep` gebruiken in een pijplijn

In het volgende voorbeeld wordt de uitvoer gebruikt van `SynapseSparkStep` de die u in de vorige sectie hebt [gemaakt.](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool) Andere stappen in de pijplijn hebben mogelijk hun eigen unieke omgevingen en worden uitgevoerd op verschillende rekenbronnen die geschikt zijn voor de taak. In het voorbeeldnote notebook wordt de 'trainingsstap' uitgevoerd op een klein CPU-cluster:

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

Met de bovenstaande code wordt zo nodig de nieuwe rekenresource gemaakt. Vervolgens wordt het `step1_output` resultaat geconverteerd naar invoer voor de trainingsstap. De `as_download()` optie betekent dat de gegevens worden verplaatst naar de rekenresource, wat resulteert in snellere toegang. Als de gegevens zo groot zijn dat ze niet op de lokale rekenharde schijf passen, gebruikt u de optie om de gegevens te streamen via het `as_mount()` FUSE-bestandssysteem. De `compute_target` van deze tweede stap is , niet de resource die u hebt gebruikt in de `'cpucluster'` `'link1-spark01'` gegevensvoorbereidingsstap. In deze stap wordt een eenvoudig programma `train.py` gebruikt in plaats van het programma dat u in de vorige stap hebt `dataprep.py` gebruikt. U kunt de details van `train.py` bekijken in het voorbeeldnoteboek.

Zodra u al uw stappen hebt gedefinieerd, kunt u uw pijplijn maken en uitvoeren. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Met de bovenstaande code maakt u een pijplijn die bestaat uit de gegevensvoorbereidingsstap op Apache Spark pools powered by Azure Synapse Analytics ( `step_1` ) en de trainingsstap ( `step_2` ). Azure berekent de uitvoeringsgrafiek door de gegevensafhankelijkheden tussen de stappen te onderzoeken. In dit geval is er slechts een eenvoudige afhankelijkheid die noodzakelijkerwijs `step2_input` vereist `step1_output` is.

Met de aanroep van wordt, indien nodig, een experiment met de naam gemaakt en asynchroon een `pipeline.submit` `synapse-pipeline` Run gestart. Afzonderlijke stappen in de pijplijn worden uitgevoerd als onderliggende runs van deze hoofduitleiding en kunnen worden bewaakt en gecontroleerd op de pagina Experimenten van Studio.

## <a name="next-steps"></a>Volgende stappen

* [Pijplijnen publiceren machine learning bijhouden](how-to-deploy-pipelines.md) 
* [Azure Machine Learning bewaken](monitor-azure-machine-learning.md)
* [Geautomatiseerde ML gebruiken in een Azure Machine Learning pijplijn in Python](how-to-use-automlstep-in-pipelines.md)
