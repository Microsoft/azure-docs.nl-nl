---
title: Apache Spark gebruiken in een machine learning pijp lijn (preview-versie)
titleSuffix: Azure Machine Learning
description: Koppel uw Synapse-werk ruimte aan uw Azure machine learning-pijp lijn om Spark te gebruiken voor het bewerken van gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/25/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a912bc5abcdadf3f8eca46f805c433d3a1058c68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662551"
---
# <a name="how-to-use-apache-spark-in-your-machine-learning-pipeline-with-azure-synapse-preview"></a>Apache Spark in uw machine learning-pijp lijn gebruiken met Azure Synapse (preview)

In dit artikel leert u hoe u Apache Spark Pools kunt gebruiken die door Synapse worden ondersteund als reken doel voor een gegevens voorbereidings stap in een Azure Machine Learning-pijp lijn. U leert hoe u met één pijp lijn reken resources kunt gebruiken die geschikt zijn voor de specifieke stap, zoals gegevens voorbereiding of training. U ziet hoe gegevens worden voor bereid voor de Spark-stap en hoe deze worden door gegeven aan de volgende stap. 

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure machine learning-werk ruimte](how-to-manage-workspace.md) om al uw pijplijn resources te bevatten.

* [Configureer uw ontwikkel omgeving](how-to-configure-environment.md) om de Azure machine learning SDK te installeren, of gebruik een [Azure machine learning Compute-exemplaar](concept-compute-instance.md) waarbij de SDK al is geïnstalleerd.

* Een Synapse-werk ruimte en Apache Spark pool maken (Zie [Quick Start: een serverloze Apache Spark groep maken met behulp van Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-machine-learning-workspace-and-synapse-workspace"></a>Uw machine learning-werk ruimte en Synapse-werk ruimte koppelen 

U kunt uw Apache Spark groepen maken en beheren in een Synapse-werk ruimte. Als u een Spark-groep wilt integreren met een Azure Machine Learning-werk ruimte, moet u een koppeling maken naar de Synapse-werk ruimte. 

U kunt een Synapse Spark-pool koppelen via Azure Machine Learning Studio-gebruikers interface met behulp van de pagina **gekoppelde services** . U kunt dit ook doen via de **berekenings** pagina met de optie **Compute-koppeling** .

U kunt ook een Synapse Spark-pool koppelen via SDK (zoals hieronder wordt beschreven) of via een ARM-sjabloon (zie dit [voor beeld arm-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

U kunt de opdracht regel gebruiken om de ARM-sjabloon te volgen, de gekoppelde service toe te voegen en de Synapse-groep te koppelen aan de volgende code:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Als u een koppeling wilt maken naar de Synapse-werk ruimte, moet u de rol eigenaar hebben in de Synapse werkruimte resource. Controleer uw toegang in de Azure Portal.
> Bij het maken van de gekoppelde service wordt een door het systeem toegewezen identiteit (SAI) opgehaald. U moet deze koppelings service SAI de rol ' Synapse Apache Spark Administrator ' van Synapse Studio, zodat de Spark-taak kan worden verzonden (Zie [How to manage Synapse RBAC Role Assignments in Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)) (Engelstalig). U moet ook de gebruiker van de Azure Machine Learning werk ruimte de rol ' Inzender ' toekennen vanuit Azure Portal van resource beheer.

## <a name="create-or-retrieve-the-link-between-your-synapse-workspace-and-your-azure-machine-learning-workspace"></a>De koppeling tussen uw Synapse-werk ruimte en uw Azure Machine Learning-werk ruimte maken of ophalen

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

## <a name="attach-your-synapse-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Uw Synapse Spark-pool als reken doel voor Azure Machine Learning koppelen

Als u uw Synapse Spark-pool wilt gebruiken om een stap in uw machine learning pijp lijn uit te scha kelen, moet u deze koppelen als een `ComputeTarget` voor de pijplijn stap, zoals wordt weer gegeven in de volgende code.

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

De eerste stap is het configureren van de `SynapseCompute` . Het `linked_service` argument is het `LinkedService` object dat u in de vorige stap hebt gemaakt of opgehaald. Het `type` argument moet zijn `SynapseSpark` . Het `pool_name` argument in `SynapseCompute.attach_configuration()` moet overeenkomen met dat van een bestaande groep in uw Synapse-werk ruimte. Voor meer informatie over het maken van een Apache Spark-groep in de werk ruimte Synapse raadpleegt [u Quick Start: een groep zonder server Apache Spark maken met behulp van Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). Het type `attach_config` is `ComputeTargetAttachConfiguration` .

Zodra de configuratie is gemaakt, maakt u een machine learning `ComputeTarget` door door te geven in de `Workspace` , `ComputeTargetAttachConfiguration` en de naam waarmee u wilt verwijzen naar de reken kracht in de werk ruimte machine learning. De aanroep `ComputeTarget.attach()` is asynchroon, waardoor de voorbeeld blokken worden geblokkeerd totdat de aanroep is voltooid.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Maak een `SynapseSparkStep` die gebruikmaakt van de gekoppelde Apache Spark-pool

Met het voor beeld van een notebook [Spark-taak in een Synapse Spark-pool](https://github.com/azure/machinelearningnotebooks) definieert u een eenvoudige machine learning-pijp lijn. Eerst definieert het notitie blok een stap voor gegevens voorbereiding die wordt ondersteund door de `synapse_compute` definitie in de vorige stap. Vervolgens definieert het notitie blok een trainings stap die wordt ondersteund door een reken doel dat beter geschikt is voor training. De voorbeeld notitieblok maakt gebruik van de Titanic-data base om gegevens invoer en-uitvoer te demonstreren. de gegevens worden niet daad werkelijk opgeschoond of maken een voorspellend model. Omdat er in dit voor beeld geen echte training is, gebruikt de trainings stap een goedkope, op CPU gebaseerde Compute-resource.

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

Naast gegevens kan een pijplijn stap bestaan uit python-afhankelijkheden per stap. Afzonderlijke `SynapseSparkStep` objecten kunnen ook hun nauw keurige Synapse-configuratie opgeven. Dit wordt weer gegeven in de volgende code, waarmee wordt aangegeven dat de `azureml-core` pakket versie mini maal moet zijn `1.20.0` . (Zoals eerder vermeld, is deze vereiste `azureml-core` vereist voor het gebruik van een `FileDataset` als invoer.)

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

De volgende set argumenten voor de `SynapseSparkStep` constructor Control Apache Spark. De `compute_target` is de `'link1-spark01'` die we eerder als reken doel hebben gekoppeld. Met de andere para meters geeft u het geheugen en de kernen op die u wilt gebruiken.

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

Dit script voor gegevens voorbereiding voert geen echte gegevens transformatie uit, maar illustreert hoe u gegevens ophaalt, converteert naar een Spark-data frame en hoe u een eenvoudige Spark-manipulatie uitvoert. U kunt de uitvoer in Azure Machine Learning Studio vinden door de onderliggende uitvoering te openen, het tabblad **uitvoer en logboeken** te kiezen en het `logs/azureml/driver/stdout` bestand te openen, zoals wordt weer gegeven in de volgende afbeelding.

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

```
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Met de bovenstaande code maakt u een pijp lijn die bestaat uit de stap voor het voorbereiden van gegevens, gemaakt door Synapse ( `step_1` ) en de stap training ( `step_2` ). Azure berekent de uitvoerings grafiek door de gegevens afhankelijkheden tussen de stappen te controleren. In dit geval is er slechts een eenvoudige afhankelijkheid die `step2_input` per se vereist is `step1_output` .

De aanroep om `pipeline.submit` , indien nodig, een experiment te maken dat binnen het proces wordt aangeroepen `synapse-pipeline` en wordt asynchroon gestart. Afzonderlijke stappen in de pijp lijn worden uitgevoerd als onderliggend item van deze hoofd uitvoering en kunnen worden gecontroleerd en gecontroleerd op de pagina experimenten van Studio.

## <a name="next-steps"></a>Volgende stappen

* [machine learning pijp lijnen publiceren en bijhouden](how-to-deploy-pipelines.md) 
* [Azure Machine Learning bewaken](monitor-azure-machine-learning.md)
* [Automatische ML gebruiken in een Azure Machine Learning pijp lijn in python](how-to-use-automlstep-in-pipelines.md)