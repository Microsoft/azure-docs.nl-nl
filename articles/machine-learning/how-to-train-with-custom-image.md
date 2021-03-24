---
title: Een model trainen met behulp van een aangepaste docker-installatie kopie
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van uw eigen docker-installatie kopieën of van micro soft, voor het trainen van modellen in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ea282cb0fcf25f7ac5f17bb9e2f693d189320078
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889158"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Een model trainen met behulp van een aangepaste docker-installatie kopie

In dit artikel leert u hoe u een aangepaste docker-installatie kopie gebruikt wanneer u een model met Azure Machine Learning maakt. U gebruikt de voorbeeld scripts in dit artikel om huisdier installatie kopieën te classificeren door een convolutional Neural-netwerk te maken. 

Azure Machine Learning biedt een standaard installatie kopie van docker-basis. U kunt ook Azure Machine Learning omgevingen gebruiken om een andere basis installatie kopie op te geven, zoals een van de onderhanden [Azure machine learning basis installatie kopieën](https://github.com/Azure/AzureML-Containers) of uw eigen [aangepaste installatie kopie](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Aangepaste basis installatie kopieën bieden u de mogelijkheid om uw afhankelijkheden nauw keurig te beheren en de controle over onderdeel versies nauw keuriger te houden wanneer u trainings taken uitvoert.

## <a name="prerequisites"></a>Vereisten

Voer de code uit in een van de volgende omgevingen:

* Azure Machine Learning Compute-instantie (geen down loads of installatie vereist):
  * Voltooi de zelf studie [omgeving en werk ruimte instellen](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken die vooraf is geladen met de SDK en de voor beeld-opslag plaats.
  * Zoek in het Azure machine learning- [voor beeld-opslag plaats](https://github.com/Azure/azureml-examples)een voltooid notitie blok door naar de fastai-map **notebooks**  >    >  **-resnet34. ipynb** te gaan. 
* Uw eigen Jupyter Notebook-server:
  * Maak een [configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
  * Installeer de [Azure machine learning SDK](/python/api/overview/azure/ml/install). 
  * Een [Azure container Registry](../container-registry/index.yml) of een ander docker-REGI ster maken dat op internet beschikbaar is.

## <a name="set-up-a-training-experiment"></a>Een trainings experiment instellen

In deze sectie kunt u uw trainings experiment instellen door een werk ruimte te initialiseren, uw omgeving te definiëren en een compute-doel te configureren.

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) object te maken.

Maak een `Workspace` object op basis van de config.jsvoor het bestand dat u hebt gemaakt als een [vereiste](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Uw omgeving definiëren

Maak een `Environment` object en schakel docker in.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

De opgegeven basis installatie kopie in de volgende code biedt ondersteuning voor de fast.ai-bibliotheek, waarmee gedistribueerde diep gaande mogelijkheden kunnen worden uitgebreid. Zie de [opslag plaats Fast.ai docker hub](https://hub.docker.com/u/fastdotai)voor meer informatie. 

Wanneer u uw aangepaste docker-installatie kopie gebruikt, is het mogelijk dat uw python-omgeving al correct is ingesteld. In dat geval stelt u de `user_managed_dependencies` vlag in op `True` voor het gebruik van de ingebouwde python-omgeving van uw aangepaste installatie kopie. Azure Machine Learning bouwt standaard een Conda-omgeving met afhankelijkheden die u hebt opgegeven. De service voert het script in die omgeving uit in plaats van python-bibliotheken te gebruiken die u hebt geïnstalleerd op de basis installatie kopie.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Een persoonlijk container register gebruiken (optioneel)

Als u een installatie kopie wilt gebruiken uit een persoonlijk container register dat zich niet in uw werk ruimte bevindt, gebruikt `docker.base_image_registry` u om het adres van de opslag plaats en een gebruikers naam en wacht woord op te geven:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Een aangepaste Dockerfile gebruiken (optioneel)

Het is ook mogelijk om een aangepaste Dockerfile te gebruiken. Gebruik deze methode als u niet-python-pakketten als afhankelijkheden moet installeren. Vergeet niet om de basis installatie kopie in te stellen op `None` .

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
> Azure Machine Learning ondersteunt alleen docker-installatie kopieën die de volgende software bieden:
> * Ubuntu 16,04 of hoger.
> * Conda 4.5. # of hoger.
> * Python 3.6 +.

Zie [software omgevingen maken en gebruiken](how-to-use-environments.md)voor meer informatie over het maken en beheren van Azure machine learning omgevingen. 

### <a name="create-or-attach-a-compute-target"></a>Een compute-doel maken of koppelen

U moet een [reken doel](concept-azure-machine-learning-architecture.md#compute-targets) maken voor het trainen van uw model. In deze zelf studie maakt u `AmlCompute` als uw trainings berekenings bron.

Het maken van `AmlCompute` een paar minuten duurt. Als de `AmlCompute` resource zich al in uw werk ruimte bevindt, slaat deze code het aanmaak proces over.

Net als bij andere Azure-Services gelden er limieten voor bepaalde bronnen (bijvoorbeeld `AmlCompute` ) die aan de Azure machine learning-service zijn gekoppeld. Zie [standaard limieten en een hoger quotum aanvragen](how-to-manage-quotas.md)voor meer informatie.

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

## <a name="configure-your-training-job"></a>Uw trainings taak configureren

Voor deze zelf studie gebruikt u het trainings script *Train.py* in [github](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py). In de praktijk kunt u een aangepast trainings script maken en dit uitvoeren, net als bij Azure Machine Learning.

Maak een `ScriptRunConfig` resource om uw taak te configureren voor uitvoering op het gewenste [Compute-doel](how-to-set-up-training-targets.md).

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Uw trainings taak verzenden

Wanneer u een trainings uitvoering verzendt met behulp van een- `ScriptRunConfig` object, `submit` retourneert de methode een object van het type `ScriptRun` . Het geretourneerde `ScriptRun` object biedt u programmatische toegang tot informatie over de uitvoering van de training. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning trainings scripts worden uitgevoerd door de hele bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [. ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neemt u het niet op in de bron directory. In plaats daarvan kunt u toegang krijgen tot uw gegevens met behulp van een gegevens [opslag](/python/api/azureml-core/azureml.data).

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een model getraind met behulp van een aangepaste docker-installatie kopie. Raadpleeg de volgende artikelen voor meer informatie over Azure Machine Learning:
* [Metrische uitvoerings gegevens](how-to-track-experiments.md) tijdens de training volgen.
* [Implementeer een model](how-to-deploy-custom-docker-image.md) met behulp van een aangepaste docker-installatie kopie.
