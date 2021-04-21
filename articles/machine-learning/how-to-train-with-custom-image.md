---
title: Een model trainen met behulp van een aangepaste Docker-afbeelding
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van uw eigen Docker-afbeeldingen, of gecureerde afbeeldingen van Microsoft, om modellen te trainen in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 953d43f93635e25da008515afd9baf9a9e9b7afa
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817067"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Een model trainen met behulp van een aangepaste Docker-afbeelding

In dit artikel leert u hoe u een aangepaste Docker-afbeelding gebruikt wanneer u modellen traint met Azure Machine Learning. U gebruikt de voorbeeldscripts in dit artikel om afbeeldingen van huisdieren te classificeren door een convolutioneel neuraal netwerk te maken. 

Azure Machine Learning biedt een standaard Docker-basisafbeelding. U kunt ook Azure Machine Learning gebruiken om een andere basisafbeelding op te geven, zoals een van de onderhouden [Azure Machine Learning](https://github.com/Azure/AzureML-Containers) basisafbeeldingen of uw eigen [aangepaste afbeelding.](how-to-deploy-custom-docker-image.md#create-a-custom-base-image) Met aangepaste basisafbeeldingen kunt u uw afhankelijkheden nauwkeurig beheren en de controle over onderdeelversies bij het uitvoeren van trainingstaken nauwkeurig behouden.

## <a name="prerequisites"></a>Vereisten

Voer de code uit op een van deze omgevingen:

* Azure Machine Learning rekenproces (geen downloads of installatie vereist):
  * Voltooi de [zelfstudie Omgeving en werkruimte instellen](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
  * Zoek in [Azure Machine Learning](https://github.com/Azure/azureml-examples)opslagplaats met voorbeelden een voltooid notebook door naar de map   >  **notebooks fastai**  >  **train-pets-resnet34.ipynb te** gaan. 
* Uw eigen Jupyter Notebook server:
  * Maak een [configuratiebestand voor de werkruimte.](how-to-configure-environment.md#workspace)
  * Installeer de [Azure Machine Learning SDK](/python/api/overview/azure/ml/install). 
  * Maak een [Azure-containerregister](../container-registry/index.yml) of een ander Docker-register dat beschikbaar is op internet.

## <a name="set-up-a-training-experiment"></a>Een trainingsexperiment instellen

In deze sectie stelt u uw trainingsexperiment in door een werkruimte te initialiseren, uw omgeving te definiëren en een rekendoel te configureren.

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning is](concept-workspace.md) de resource op het hoogste niveau voor de service. Het biedt u een centrale plaats om te werken met alle artefacten die u maakt. In de Python-SDK hebt u toegang tot de werkruimteartefacten door een -object te [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) maken.

Maak een `Workspace` -object op basis van config.js-bestand dat u als vereiste hebt [gemaakt.](#prerequisites)

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Uw omgeving definiëren

Maak een `Environment` -object en schakel Docker in.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

De opgegeven basisafbeelding in de volgende code ondersteunt de fast.ai bibliotheek, die gedistribueerde deep learning-mogelijkheden biedt. Zie de opslagplaats fast.ai Docker Hub [meer informatie.](https://hub.docker.com/u/fastdotai) 

Wanneer u uw aangepaste Docker-afbeelding gebruikt, is uw Python-omgeving mogelijk al goed ingesteld. Stel in dat geval de vlag `user_managed_dependencies` in op om de ingebouwde Python-omgeving van uw aangepaste `True` afbeelding te gebruiken. Standaard wordt Azure Machine Learning Conda-omgeving gemaakt met afhankelijkheden die u hebt opgegeven. De service voert het script uit in die omgeving in plaats van python-bibliotheken te gebruiken die u op de basisafbeelding hebt geïnstalleerd.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Een privécontainerregister gebruiken (optioneel)

Als u een afbeelding wilt gebruiken uit een privécontainerregister dat zich niet in uw werkruimte, gebruikt u om het adres van de opslagplaats en een gebruikersnaam en `docker.base_image_registry` wachtwoord op te geven:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Een aangepaste Dockerfile gebruiken (optioneel)

Het is ook mogelijk om een aangepast Dockerfile te gebruiken. Gebruik deze methode als u niet-Python-pakketten als afhankelijkheden moet installeren. Vergeet niet om de basisafbeelding in te stellen op `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning ondersteunt alleen Docker-afbeeldingen die de volgende software bieden:
> * Ubuntu 16.04 of hoger.
> * Conda 4.5.# of hoger.
> * Python 3.6+.

Zie Softwareomgevingen maken en gebruiken voor meer Azure Machine Learning over het maken en beheren [van omgevingen.](how-to-use-environments.md) 

### <a name="create-or-attach-a-compute-target"></a>Een rekendoel maken of koppelen

U moet een rekendoel [maken om](concept-azure-machine-learning-architecture.md#compute-targets) uw model te trainen. In deze zelfstudie maakt u als `AmlCompute` uw trainingsrekenresource.

Het maken `AmlCompute` van duurt enkele minuten. Als de `AmlCompute` resource al in uw werkruimte staat, slaat deze code het aanmaakproces over.

Net als bij andere Azure-services gelden er limieten voor bepaalde resources (bijvoorbeeld `AmlCompute` ) die zijn gekoppeld aan de Azure Machine Learning Service. Zie Standaardlimieten en een hoger quotum aanvragen voor [meer informatie.](how-to-manage-quotas.md)

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Uw trainings job configureren

Voor deze zelfstudie gebruikt u het trainingsscript *train.py* op [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py). In de praktijk kunt u elk aangepast trainingsscript uitvoeren, zoals dat is, met Azure Machine Learning.

Maak een `ScriptRunConfig` resource om uw taak te configureren voor het uitvoeren op het gewenste [rekendoel.](how-to-set-up-training-targets.md)

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Uw trainings job verzenden

Wanneer u een trainingsrun indient met behulp van een `ScriptRunConfig` -object, `submit` retourneert de methode een object van het type `ScriptRun` . Het `ScriptRun` geretourneerde object biedt u programmatische toegang tot informatie over de trainingsrun. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning voert trainingsscripts uit door de volledige bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [.ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neem u het niet op in de bronmap. Gebruik in plaats daarvan een gegevensstore om toegang te krijgen [tot uw gegevens.](/python/api/azureml-core/azureml.data)

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een model getraind met behulp van een aangepaste Docker-afbeelding. Zie deze andere artikelen voor meer informatie over Azure Machine Learning:
* [Metrische gegevens van de run bijhouden](how-to-log-view-metrics.md) tijdens de training.
* [Implementeer een model](how-to-deploy-custom-docker-image.md) met behulp van een aangepaste Docker-afbeelding.
