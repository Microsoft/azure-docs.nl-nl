---
title: Apache Spark gebruiken in een machine learning pijp lijn (preview-versie)
titleSuffix: Azure Machine Learning
description: Koppel uw Azure Synapse Analytics-werk ruimte aan uw Azure machine learning-pijp lijn om Apache Spark te gebruiken voor het bewerken van gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 1dc4e0b70b0d39d01bada26992eb2213c1e855c5
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455056"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Apache Spark (mogelijk gemaakt door Azure Synapse Analytics) in uw machine learning-pijp lijn (preview) gebruiken

In dit artikel leert u hoe u Apache Spark Pools kunt gebruiken die door Azure Synapse Analytics worden aangedreven als het reken doel voor een gegevens voorbereidings stap in een Azure Machine Learning-pijp lijn. U leert hoe u met één pijp lijn reken resources kunt gebruiken die geschikt zijn voor de specifieke stap, zoals gegevens voorbereiding of training. U ziet hoe gegevens worden voor bereid voor de Spark-stap en hoe deze worden door gegeven aan de volgende stap. 

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure machine learning-werk ruimte](how-to-manage-workspace.md) om al uw pijplijn resources te bevatten.

* [Configureer uw ontwikkel omgeving](how-to-configure-environment.md) om de Azure machine learning SDK te installeren, of gebruik een [Azure machine learning Compute-exemplaar](concept-compute-instance.md) waarbij de SDK al is geïnstalleerd.

* Een Azure Synapse Analytics-werk ruimte en een Apache Spark pool maken (Zie [Quick Start: een serverloze Apache Spark groep maken met behulp van Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Uw Azure Machine Learning-werk ruimte en Azure Synapse Analytics-werk ruimte koppelen 

U kunt uw Apache Spark groepen maken en beheren in een Azure Synapse Analytics-werk ruimte. Als u een Apache Spark groep wilt integreren met een Azure Machine Learning-werk ruimte, moet u een [koppeling maken naar de Azure Synapse Analytics-werk ruimte](how-to-link-synapse-ml-workspaces.md). 

U kunt een Apache Spark-groep koppelen via Azure Machine Learning Studio-gebruikers interface met behulp van de pagina **gekoppelde services** . U kunt dit ook doen via de **berekenings** pagina met de optie **Compute-koppeling** .

U kunt ook een Apache Spark groep koppelen via SDK (zoals hieronder wordt beschreven) of via een ARM-sjabloon (zie dit [voor beeld arm-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

U kunt de opdracht regel gebruiken om de ARM-sjabloon te volgen, de gekoppelde service toe te voegen en de Apache Spark groep te koppelen aan de volgende code:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Als u een koppeling wilt maken naar de Azure Synapse Analytics-werk ruimte, moet u de rol eigenaar hebben in de Azure Synapse Analytics-werkruimte resource. Controleer uw toegang in de Azure Portal.
> Bij het maken van de gekoppelde service wordt een door het systeem toegewezen identiteit (SAI) opgehaald. U moet deze koppelings service SAI de rol ' Synapse Apache Spark Administrator ' van Synapse Studio, zodat de Spark-taak kan worden verzonden (Zie [How to manage Synapse RBAC Role Assignments in Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)) (Engelstalig). U moet ook de gebruiker van de Azure Machine Learning werk ruimte de rol ' Inzender ' toekennen vanuit Azure Portal van resource beheer.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>De koppeling tussen uw Azure Synapse Analytics-werk ruimte en uw Azure Machine Learning-werk ruimte maken of ophalen

U kunt gekoppelde services in uw werk ruimte ophalen met de volgende code:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Eerst `Workspace.from_config()` opent u uw Azure machine learning-werk ruimte met behulp van de configuratie in `config.json` (Zie [zelf studie: aan de slag met Azure machine learning in uw ontwikkel omgeving](tutorial-1st-experiment-sdk-setup-local.md)). Vervolgens worden alle gekoppelde services die beschikbaar zijn in de werk ruimte afgedrukt met de code. Ten slotte `LinkedService.get()` haalt een gekoppelde service op met de naam `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Koppel uw Apache Spark-pool als een reken doel voor Azure Machine Learning

Als u uw Apache Spark-pool wilt gebruiken om een stap in uw machine learning pijp lijn uit te scha kelen, moet u deze koppelen als een `ComputeTarget` voor de pijplijn stap, zoals wordt weer gegeven in de volgende code.

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

De eerste stap is het configureren van de `SynapseCompute` . Het `linked_service` argument is het `LinkedService` object dat u in de vorige stap hebt gemaakt of opgehaald. Het `type` argument moet zijn `SynapseSpark` . Het `pool_name` argument in `SynapseCompute.attach_configuration()` moet overeenkomen met dat van een bestaande groep in uw Azure Synapse Analytics-werk ruimte. Voor meer informatie over het maken van een Apache Spark-groep in de Azure Synapse Analytics-werk ruimte raadpleegt [u Quick Start: een groep zonder server Apache Spark maken met behulp van Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). Het type `attach_config` is `ComputeTargetAttachConfiguration` .

Zodra de configuratie is gemaakt, maakt u een machine learning `ComputeTarget` door door te geven in de `Workspace` , `ComputeTargetAttachConfiguration` en de naam waarmee u wilt verwijzen naar de reken kracht in de werk ruimte machine learning. De aanroep `ComputeTarget.attach()` is asynchroon, waardoor de voorbeeld blokken worden geblokkeerd totdat de aanroep is voltooid.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Maak een `SynapseSparkStep` die gebruikmaakt van de gekoppelde Apache Spark pool

Gegevens stromen naar een machine learning pijp lijn via `DatasetConsumptionConfig` objecten, die tabellaire gegevens of sets bestanden kunnen bevatten. De gegevens zijn vaak afkomstig uit bestanden in Blob Storage in de gegevens opslag van een werk ruimte. De volgende code toont een aantal typische code voor het maken van een invoer voor een machine learning-pijp lijn:

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

In de bovenstaande code wordt ervan uitgegaan dat het bestand `Titanic.csv` zich in de Blob-opslag bevindt. De code laat zien hoe u het bestand als een `TabularDataset` en als kunt lezen `FileDataset` . Deze code is alleen bedoeld voor demonstratie doeleinden, omdat het verwarrend is om invoer te dupliceren of om een enkele gegevens bron te interpreteren als een tabel met resources en net als een bestand.

> [!Important]
> Als u een `FileDataset` as-invoer wilt gebruiken, `azureml-core` moet uw versie mini maal zijn `1.20.0` . Hier wordt beschreven hoe u dit kunt opgeven met behulp van de- `Environment` klasse.

Wanneer een stap is voltooid, kunt u ervoor kiezen om uitvoer gegevens op te slaan met behulp van code, vergelijkbaar met:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

In dit geval worden de gegevens opgeslagen in de `datastore` in een bestand met de naam `test` en zijn ze beschikbaar in de machine learning-werk ruimte als een `Dataset` met de namen `registered_dataset` .

Naast gegevens kan een pijplijn stap bestaan uit python-afhankelijkheden per stap. Afzonderlijke `SynapseSparkStep` objecten kunnen ook de exacte Apache Spark configuratie van Azure Synapse opgeven. Dit wordt weer gegeven in de volgende code, waarmee wordt aangegeven dat de `azureml-core` pakket versie mini maal moet zijn `1.20.0` . (Zoals eerder vermeld, is deze vereiste `azureml-core` vereist voor het gebruik van een `FileDataset` als invoer.)

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

De bovenstaande code bevat één stap in de Azure machine learning-pijp lijn. In deze stap `environment` wordt een specifieke `azureml-core` versie opgegeven en kunnen indien nodig andere Conda-of PIP-afhankelijkheden worden toegevoegd.

De `SynapseSparkStep` wordt geladen en geüpload vanaf de lokale computer de submap `./code` . Deze map wordt opnieuw gemaakt op de Compute-Server en de stap voert het bestand `dataprep.py` uit in die map. De `inputs` en `outputs` van deze stap zijn de `step1_input1` , `step1_input2` , en `step1_output` objecten die eerder zijn besproken. De eenvoudigste manier om toegang te krijgen tot deze waarden in het `dataprep.py` script is om ze te koppelen aan de naam `arguments` .

De volgende set argumenten voor het `SynapseSparkStep` besturings element van de constructor Apache Spark. De `compute_target` is de `'link1-spark01'` die we eerder als reken doel hebben gekoppeld. Met de andere para meters geeft u het geheugen en de kernen op die u wilt gebruiken.

Het voor beeld-notebook gebruikt de volgende code voor `dataprep.py` :

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

Dit script voor gegevens voorbereiding heeft geen echte gegevens transformatie, maar illustreert hoe u gegevens ophaalt, converteert naar een Spark-data frame en hoe u een eenvoudige Apache Spark bewerking kunt uitvoeren. U kunt de uitvoer in Azure Machine Learning Studio vinden door de onderliggende uitvoering te openen, het tabblad **uitvoer en logboeken** te kiezen en het `logs/azureml/driver/stdout` bestand te openen, zoals wordt weer gegeven in de volgende afbeelding.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Scherm afbeelding van Studio met het tabblad stdout van onderliggende uitvoering":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>De `SynapseSparkStep` in een pijp lijn gebruiken

Andere stappen in de pijp lijn hebben mogelijk hun eigen unieke omgevingen en worden uitgevoerd op verschillende reken resources die relevant zijn voor de taak. Het voor beeld-notebook voert de stap ' training ' uit op een klein CPU-cluster:

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

De bovenstaande code maakt zo nodig de nieuwe reken resource. Vervolgens wordt het `step1_output` resultaat geconverteerd naar invoer voor de stap training. De `as_download()` optie betekent dat de gegevens worden verplaatst naar de reken resource, wat leidt tot snellere toegang. Als de gegevens zo groot zijn dat deze niet op de lokale Compute hard drive passen, gebruikt u de `as_mount()` optie om de gegevens te streamen via het systeem voor zekering. De `compute_target` van deze tweede stap is `'cpucluster'` , niet de `'link1-spark01'` resource die u hebt gebruikt in de stap voor het voorbereiden van gegevens. In deze stap wordt een eenvoudig programma gebruikt in `train.py` plaats van het dat `dataprep.py` u in de vorige stap hebt gebruikt. U kunt de details van `train.py` in het voorbeeld notitieblok bekijken.

Zodra u al uw stappen hebt gedefinieerd, kunt u uw pijp lijn maken en uitvoeren. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Met de bovenstaande code maakt u een pijp lijn die bestaat uit de stap voor het voorbereiden van gegevens op Apache Spark Pools van Azure Synapse Analytics ( `step_1` ) en de training Step ( `step_2` ). Azure berekent de uitvoerings grafiek door de gegevens afhankelijkheden tussen de stappen te controleren. In dit geval is er slechts een eenvoudige afhankelijkheid die `step2_input` per se vereist is `step1_output` .

De aanroep om `pipeline.submit` , indien nodig, een experiment te maken dat binnen het proces wordt aangeroepen `synapse-pipeline` en wordt asynchroon gestart. Afzonderlijke stappen in de pijp lijn worden uitgevoerd als onderliggend item van deze hoofd uitvoering en kunnen worden gecontroleerd en gecontroleerd op de pagina experimenten van Studio.

## <a name="next-steps"></a>Volgende stappen

* [machine learning pijp lijnen publiceren en bijhouden](how-to-deploy-pipelines.md) 
* [Azure Machine Learning bewaken](monitor-azure-machine-learning.md)
* [Automatische ML gebruiken in een Azure Machine Learning pijp lijn in python](how-to-use-automlstep-in-pipelines.md)
