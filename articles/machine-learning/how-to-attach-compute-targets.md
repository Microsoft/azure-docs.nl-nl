---
title: Training instellen & doel stellingen voor het afleiden van berekening
titleSuffix: Azure Machine Learning
description: Reken resources (Compute-doelen) toevoegen aan uw werk ruimte om te gebruiken voor machine learning training en deinterferentie
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 9fa6a1758bc2e2a76291efc3bb239c5249a6e21e
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149338"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Reken doelen instellen voor model training en implementatie

Meer informatie over het koppelen van Azure Compute-resources aan uw Azure Machine Learning-werk ruimte.  Vervolgens kunt u deze resources gebruiken als training en de [berekenings doelen](concept-compute-target.md) voor de inschakeling in uw machine learning taken.

In dit artikel vindt u informatie over het instellen van uw werk ruimte voor het gebruik van deze reken bronnen:

* Uw lokale computer
* Externe virtuele machines
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container Instance

Als u Compute-doelen wilt gebruiken die worden beheerd door Azure Machine Learning, raadpleegt u:


* [Azure Machine Learning-rekeninstantie](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning-rekenclusters](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes service-cluster](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

* De [Azure cli-extensie voor machine learning service](reference-azure-machine-learning-cli.md), [Azure machine learning python SDK](/python/api/overview/azure/ml/intro)of de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Beperkingen

* **Maak niet meerdere gelijktijdige bijlagen met dezelfde reken kracht** vanuit uw werk ruimte. U kunt bijvoorbeeld één Azure Kubernetes service-cluster koppelen aan een werk ruimte met behulp van twee verschillende namen. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

    Als u een compute-doel opnieuw wilt koppelen, bijvoorbeeld om TLS of een andere configuratie-instelling voor het cluster te wijzigen, moet u eerst de bestaande bijlage verwijderen.

## <a name="whats-a-compute-target"></a>Wat is een reken doel?

Met Azure Machine Learning kunt u uw model trainen op diverse resources of omgevingen, die gezamenlijk worden aangeduid als [__Compute-doelen__](concept-azure-machine-learning-architecture.md#compute-targets). Een compute-doel kan een lokale machine of een Cloud resource zijn, zoals een Azure Machine Learning compute, Azure HDInsight of een externe virtuele machine.  U kunt ook Compute-doelen voor model implementatie gebruiken, zoals beschreven in [' waar en hoe u uw modellen implementeert '](how-to-deploy-and-where.md).


## <a name="local-computer"></a><a id="local"></a>Lokale computer

Wanneer u uw lokale computer gebruikt voor **trainingen**, hoeft u geen reken doel te maken.  U hoeft alleen maar [de training](how-to-set-up-training-targets.md) uit te voeren vanaf uw lokale computer.

Wanneer u uw lokale computer **gebruikt voor** demijnen, moet docker zijn geïnstalleerd. Als u de implementatie wilt uitvoeren, gebruikt u [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-) om de poort te definiëren die wordt gebruikt door de webservice. Gebruik vervolgens het normale implementatie proces zoals beschreven in [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Externe virtuele machines

Azure Machine Learning biedt ook ondersteuning voor het koppelen van een virtuele machine van Azure. De virtuele machine moet een Azure-Data Science Virtual Machine (DSVM) zijn. Deze VM is een vooraf geconfigureerde data Science-en AI-ontwikkel omgeving in Azure. De virtuele machine biedt een geruime keuze aan hulpprogram ma's en frameworks voor een volledige levenscyclus machine learning ontwikkeling. Zie [Configure a Development Environment](./how-to-configure-environment.md#dsvm)(Engelstalig) voor meer informatie over het gebruik van de DSVM met Azure machine learning.

1. **Maken**: Maak een DSVM voordat u het gebruikt om het model te trainen. Zie [de data Science virtual machine inrichten voor Linux (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md)als u deze resource wilt maken.

    > [!WARNING]
    > Azure Machine Learning ondersteunt alleen virtuele machines waarop **Ubuntu** wordt uitgevoerd. Wanneer u een virtuele machine maakt of een bestaande virtuele machine kiest, moet u een virtuele machine selecteren die gebruikmaakt van Ubuntu.
    > 
    > Azure Machine Learning moet de virtuele machine ook een __openbaar IP-adres__ hebben.

1. **Bijvoegen**: als u een bestaande virtuele machine als een reken doel wilt koppelen, moet u de resource-id, gebruikers naam en het wacht woord voor de virtuele machine opgeven. De resource-ID van de virtuele machine kan worden samengesteld met behulp van de abonnements-ID, naam van de resource groep en de naam van de virtuele machine met de volgende teken reeks notatie: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   U kunt de DSVM ook aan uw werk ruimte koppelen [met behulp van Azure machine learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Maak niet meerdere gelijktijdige bijlagen met dezelfde DSVM in uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

1. **Configureren**: een uitvoerings configuratie voor het DSVM Compute-doel maken. Docker en Conda worden gebruikt om de trainings omgeving op de DSVM te maken en te configureren.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight is een populair platform voor Big data-analyses. Het platform biedt Apache Spark, dat kan worden gebruikt voor het trainen van uw model.

1. **Maken**: Maak het HDInsight-cluster voordat u het gebruikt om het model te trainen. Zie [een Spark-cluster maken in hdinsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)voor informatie over het maken van een Spark in hdinsight-cluster. 

    > [!WARNING]
    > Voor Azure Machine Learning moet het HDInsight-cluster een __openbaar IP-adres__ hebben.

    Wanneer u het cluster maakt, moet u een SSH-gebruikers naam en-wacht woord opgeven. Noteer deze waarden, omdat u ze nodig hebt om HDInsight als reken doel te gebruiken.
    
    Nadat het cluster is gemaakt, maakt u een verbinding met de hostnaam \<clustername> -SSH.azurehdinsight.net, waarbij \<clustername> de naam is die u voor het cluster hebt ingesteld. 

1. **Bijvoegen**: als u een HDInsight-cluster als een reken doel wilt koppelen, moet u de resource-id, gebruikers naam en het wacht woord voor het HDInsight-cluster opgeven. De resource-ID van het HDInsight-cluster kan worden samengesteld met behulp van de abonnements-ID, naam van de resource groep en het HDInsight-cluster met de volgende teken reeks indeling: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   U kunt het HDInsight-cluster ook koppelen aan uw werk ruimte [met behulp van Azure machine learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Maak niet meerdere gelijktijdige bijlagen met dezelfde HDInsight vanuit uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

1. **Configureren**: een uitvoerings configuratie voor het HDI Compute-doel maken. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch wordt gebruikt voor het efficiënt uitvoeren van grootschalige parallelle en HPC-toepassingen (High Performance Computing) in de Cloud. AzureBatchStep kan worden gebruikt in een Azure Machine Learning-pijp lijn om taken naar een Azure Batch groep machines te verzenden.

Als u Azure Batch als een reken doel wilt koppelen, moet u de Azure Machine Learning SDK gebruiken en de volgende informatie opgeven:

-    **Azure batch Compute name**: een beschrijvende naam die moet worden gebruikt voor de compute in de werk ruimte
-    **Azure batch account naam**: de naam van het Azure batch account
-    **Resource groep**: de resource groep die het Azure batch-account bevat.

De volgende code laat zien hoe u Azure Batch als een reken doel koppelt:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Maak niet meerdere gelijktijdige bijlagen op hetzelfde Azure Batch vanuit uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks is een op Apache Spark gebaseerde omgeving in de Azure-Cloud. Het kan worden gebruikt als een reken doel met een Azure Machine Learning-pijp lijn.

Maak een Azure Databricks-werk ruimte voordat u deze gebruikt. Als u een werkruimte resource wilt maken, raadpleegt u de [taak een Spark uitvoeren op Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) document.

Als u Azure Databricks als een reken doel wilt koppelen, geeft u de volgende informatie op:

* __Databricks Compute name__: de naam die u wilt toewijzen aan deze Compute-resource.
* __Naam van de Databricks-werk ruimte__: de naam van de Azure Databricks-werk ruimte.
* __Databricks-toegangs token__: het toegangs token dat wordt gebruikt voor het verifiëren van Azure Databricks. Als u een toegangs token wilt genereren, raadpleegt u het [verificatie](/azure/databricks/dev-tools/api/latest/authentication) document.

De volgende code laat zien hoe u Azure Databricks als een compute-doel koppelt aan de Azure Machine Learning SDK (__de werk ruimte Databricks moet aanwezig zijn in hetzelfde abonnement als uw AML-werk ruimte__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Bekijk een [voor beeld](https://aka.ms/pl-databricks) van een notebook op github voor een meer gedetailleerd voor beeld.

> [!WARNING]
> Maak niet meerdere gelijktijdige bijlagen op hetzelfde Azure Databricks vanuit uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics is een big data Analytics-platform in de Azure-Cloud. Het kan worden gebruikt als een reken doel met een Azure Machine Learning-pijp lijn.

Maak een Azure Data Lake Analytics-account voordat u het gebruikt. Zie aan de [slag met Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) document om deze resource te maken.

Als u Data Lake Analytics als een reken doel wilt koppelen, moet u de Azure Machine Learning SDK gebruiken en de volgende informatie opgeven:

* __Compute name__: de naam die u wilt toewijzen aan deze Compute-resource.
* __Resource groep__: de resource groep die het data Lake Analytics-account bevat.
* __Account naam__: de naam van het data Lake Analytics-account.

De volgende code laat zien hoe u Data Lake Analytics als een reken doel koppelt:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Bekijk een [voor beeld](https://aka.ms/pl-adla) van een notebook op github voor een meer gedetailleerd voor beeld.

> [!WARNING]
> Maak niet meerdere gelijktijdige bijlagen met dezelfde ADLA in uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

> [!TIP]
> Azure Machine Learning pijp lijnen kunnen alleen worden gebruikt voor gegevens die zijn opgeslagen in de standaard gegevens opslag van het Data Lake Analytics-account. Als de gegevens waarmee u wilt werken, zich in een niet-standaard archief bevindt, kunt u een gebruiken [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep) om de gegevens te kopiëren voor de training.

## <a name="azure-container-instance"></a><a id="aci"></a>Azure-container exemplaar

Azure Container Instances (ACI) worden dynamisch gemaakt wanneer u een model implementeert. U kunt geen ACI op een andere manier maken of koppelen aan uw werk ruimte. Zie [een model implementeren op Azure container instances](how-to-deploy-azure-container-instance.md)voor meer informatie.

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes service (AKS) biedt diverse configuratie opties voor het gebruik van Azure Machine Learning. Zie de [Azure Kubernetes-service maken en koppelen](how-to-create-attach-kubernetes.md)voor meer informatie.


## <a name="notebook-examples"></a>Voor beelden van notebooks

Bekijk deze notebooks voor voor beelden van training met verschillende Compute-doelen:
* [procedures voor het gebruik van azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [zelf studies/img-Classification-part1-training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* Gebruik de compute-resource om [een trainings uitvoering te configureren en](how-to-set-up-training-targets.md)in te dienen.
* [Zelf studie: een model trainen](tutorial-train-models-with-aml.md) maakt gebruik van een beheerd Compute-doel om een model te trainen.
* Meer informatie over hoe u [Hyper parameters efficiënt kunt afstemmen](how-to-tune-hyperparameters.md) om betere modellen te bouwen.
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* [Azure Machine Learning gebruiken met virtuele netwerken van Azure](./how-to-network-security-overview.md)