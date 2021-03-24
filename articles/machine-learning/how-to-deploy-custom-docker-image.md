---
title: Modellen implementeren met een aangepaste docker-installatie kopie
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een aangepaste docker-basis installatie kopie voor het implementeren van uw Azure Machine Learning-modellen. Hoewel Azure Machine Learning een standaard basis installatie kopie voor u biedt, kunt u ook uw eigen basis installatie kopie gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: fb6d9a1a1ad341763c205a11b7a6a9acafda1ac4
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889736"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Een model implementeren met behulp van een aangepaste docker-basis installatie kopie

Meer informatie over het gebruik van een aangepaste docker-basis installatie kopie bij het implementeren van getrainde modellen met Azure Machine Learning.

In Azure Machine Learning wordt een standaard basis-docker-installatie kopie gebruikt als er geen is opgegeven. U kunt de specifieke docker-installatie kopie vinden die wordt gebruikt met `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . U kunt ook Azure Machine Learning __omgevingen__ gebruiken om een specifieke basis installatie kopie te selecteren, of een aangepaste versie gebruiken die u opgeeft.

Een basis installatie kopie wordt gebruikt als uitgangs punt wanneer er een installatie kopie voor een implementatie wordt gemaakt. Het biedt het onderliggende besturings systeem en onderdelen. Het implementatie proces voegt vervolgens extra onderdelen, zoals uw model, Conda-omgeving en andere assets, toe aan de installatie kopie.

Normaal gesp roken maakt u een aangepaste basis installatie kopie wanneer u docker wilt gebruiken voor het beheren van uw afhankelijkheden, het behoud van de controle over onderdeel versies en het besparen van tijd tijdens de implementatie. Het is ook mogelijk dat u software wilt installeren die vereist is voor uw model, waarbij het installatie proces veel tijd in beslag neemt. Het installeren van de software bij het maken van de basis installatie kopie betekent dat u deze niet voor elke implementatie hoeft te installeren.

> [!IMPORTANT]
> Wanneer u een model implementeert, kunt u de kern onderdelen, zoals de webserver of de IoT Edge onderdelen, niet overschrijven. Deze onderdelen bieden een bekende werk omgeving die wordt getest en ondersteund door micro soft.

> [!WARNING]
> Micro soft kan niet helpen bij het oplossen van problemen die zijn veroorzaakt door een aangepaste installatie kopie. Als u problemen ondervindt, wordt u mogelijk gevraagd om de standaard installatie kopie of een van de installatie kopieën te gebruiken die micro soft biedt om te controleren of het probleem specifiek is voor uw installatie kopie.

Dit document is onderverdeeld in twee secties:

* Een aangepaste basis installatie kopie maken: bevat informatie over beheerders en DevOps over het maken van een aangepaste installatie kopie en het configureren van verificatie voor een Azure Container Registry met behulp van de Azure CLI en Machine Learning CLI.
* Een model implementeren met behulp van een aangepaste basis installatie kopie: hierin vindt u informatie over gegevens wetenschappers en DevOps/ML-technici voor het gebruik van aangepaste installatie kopieën bij het implementeren van een getraind model vanuit de python-SDK of ML CLI.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie het artikel [een werk ruimte maken](how-to-manage-workspace.md) voor meer informatie.
* De [Azure machine learning SDK](/python/api/overview/azure/ml/install). 
* De [Azure CLI](/cli/azure/install-azure-cli).
* De [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).
* Een [Azure container Registry](../container-registry/index.yml) of een ander docker-REGI ster dat toegankelijk is op internet.
* Bij de stappen in dit document wordt ervan uitgegaan dat u bekend bent met het maken en gebruiken van een inkomend __configuratie__ object als onderdeel van de implementatie van het model. Zie voor meer informatie [waar u kunt implementeren en hoe](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Een aangepaste basis installatie kopie maken

In de informatie in deze sectie wordt ervan uitgegaan dat u een Azure Container Registry gebruikt voor het opslaan van docker-installatie kopieën. Gebruik de volgende controle lijst bij het plannen van het maken van aangepaste installatie kopieën voor Azure Machine Learning:

* Gebruikt u de Azure Container Registry die is gemaakt voor de Azure Machine Learning-werk ruimte of een zelfstandige Azure Container Registry?

    Wanneer u installatie kopieën gebruikt die zijn opgeslagen in het __container register voor de werk ruimte__, hoeft u zich niet te verifiëren bij het REGI ster. Verificatie wordt verwerkt door de werk ruimte.

    > [!WARNING]
    > De Azure Container Registry voor uw werk ruimte wordt __gemaakt wanneer u voor het eerst een model traint of implementeert__ met behulp van de werk ruimte. Als u een nieuwe werk ruimte hebt gemaakt, maar niet hebt getraind of een model hebt gemaakt, bestaat er geen Azure Container Registry voor de werk ruimte.

    Wanneer u installatie kopieën gebruikt die zijn opgeslagen in een __zelfstandig container register__, moet u een Service-Principal configureren die ten minste lees toegang heeft. Vervolgens geeft u de Service-Principal-ID (gebruikers naam) en het wacht woord op voor iedereen die gebruikmaakt van installatie kopieën uit het REGI ster. De uitzonde ring hierop is als u het container register openbaar toegankelijk maakt.

    Zie [een persoonlijk container register maken](../container-registry/container-registry-get-started-azure-cli.md)voor meer informatie over het maken van een persoonlijke Azure container Registry.

    Zie voor meer informatie over het gebruik van service-principals met Azure Container Registry [Azure container Registry verificatie met Service-principals](../container-registry/container-registry-auth-service-principal.md).

* Azure Container Registry en installatie kopie-informatie: Geef de naam van de installatie kopie op die moet worden gebruikt door iedereen. Bijvoorbeeld, er `myimage` wordt verwezen naar een installatie kopie met de naam, opgeslagen in een REGI ster `myregistry` `myregistry.azurecr.io/myimage` met de naam, wanneer de installatie kopie wordt gebruikt voor model implementatie

### <a name="image-requirements"></a>Vereisten voor installatiekopieën

Azure Machine Learning ondersteunt alleen docker-installatie kopieën die de volgende software bieden:
* Ubuntu 16,04 of hoger.
* Conda 4.5. # of hoger.
* Python 3.6 +.

Als u gegevens sets wilt gebruiken, moet u het libzeker-dev-pakket installeren. Zorg er ook voor dat u eventueel benodigde gebruikers ruimte-pakketten installeert.

Azure ML houdt een set van CPU-en GPU-basis installatie kopieën die zijn gepubliceerd naar micro soft Container Registry die u eventueel kunt gebruiken (of referentie) in plaats van uw eigen aangepaste installatie kopie te maken. Raadpleeg de GitHub-opslag plaats [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) om de Dockerfiles voor die afbeeldingen te bekijken.

Voor GPU-installatie kopieën biedt Azure ML momenteel zowel cuda9-als cuda10-basis installatie kopieën. De belangrijkste afhankelijkheden die zijn geïnstalleerd in deze basis installatie kopieën zijn:

| Afhankelijkheden | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 |
| mpi | intelmpi = = 2018.3.222 |openmpi = = 3.1.2 |intelmpi = = 2018.3.222| openmpi = = 3.1.2 |
| CUDA | - | - | 9.0/10.0 | 9.0/10,0/10.1 |
| cudnn | - | - | 7.4/7,5 | 7.4/7,5 |
| nccl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

De CPU-installatie kopieën zijn gebouwd op basis van Ubuntu 16.04. De GPU-installatie kopieën voor cuda9 zijn gebouwd op basis van NVIDIA/CUDA: 9.0-cudnn7-devel-Ubuntu 16.04. De GPU-installatie kopieën voor cuda10 zijn gebouwd op basis van NVIDIA/CUDA: 10.0-cudnn7-devel-Ubuntu 16.04.
<a id="getname"></a>

> [!IMPORTANT]
> Wanneer u aangepaste docker-installatie kopieën gebruikt, is het raadzaam pakket versies vast te maken om de reproduceer baarheid te verbeteren.

### <a name="get-container-registry-information"></a>Container register gegevens ophalen

In deze sectie leert u hoe u de naam van de Azure Container Registry voor uw Azure Machine Learning-werk ruimte kunt ophalen.

> [!WARNING]
> De Azure Container Registry voor uw werk ruimte wordt __gemaakt wanneer u voor het eerst een model traint of implementeert__ met behulp van de werk ruimte. Als u een nieuwe werk ruimte hebt gemaakt, maar niet hebt getraind of een model hebt gemaakt, bestaat er geen Azure Container Registry voor de werk ruimte.

Als u al uw modellen hebt getraind of geïmplementeerd met behulp van Azure Machine Learning, is er een container register gemaakt voor uw werk ruimte. Als u de naam van dit container register wilt vinden, gebruikt u de volgende stappen:

1. Open een nieuwe shell of opdracht prompt en gebruik de volgende opdracht om u te verifiëren bij uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

    Volg de aanwijzingen om u te verifiëren bij het abonnement.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Gebruik de volgende opdracht om het container register voor de werk ruimte weer te geven. Vervang door `<myworkspace>` de naam van uw Azure machine learning-werk ruimte. Vervang door `<resourcegroup>` de Azure-resource groep die uw werk ruimte bevat:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    De informatie die wordt geretourneerd, is vergelijkbaar met de volgende tekst:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    De `<registry_name>` waarde is de naam van de Azure container Registry voor uw werk ruimte.

### <a name="build-a-custom-base-image"></a>Een aangepaste basis installatie kopie bouwen

Met de stappen in deze sectie wordt uitgelegd hoe u een aangepaste docker-installatie kopie maakt in uw Azure Container Registry. Zie [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) github opslag plaats) voor een voor beeld van dockerfiles.

1. Maak een nieuw tekst bestand `Dockerfile` met de naam en gebruik de volgende tekst als de inhoud:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Gebruik bij een shell of opdracht prompt het volgende om te verifiëren bij de Azure Container Registry. Vervang de `<registry_name>` door de naam van het container register waarin u de installatie kopie wilt opslaan:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Als u de Dockerfile wilt uploaden en deze wilt bouwen, gebruikt u de volgende opdracht. Vervang door `<registry_name>` de naam van het container register waarin u de installatie kopie wilt opslaan:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > In dit voor beeld wordt een tag van `:v1` toegepast op de afbeelding. Als er geen tag wordt gegeven, wordt een tag van `:latest` toegepast.

    Tijdens het bouw proces worden gegevens gestreamd naar de opdracht regel. Als de build is geslaagd, wordt er een bericht weer gegeven dat vergelijkbaar is met de volgende tekst:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Zie [een container installatie kopie bouwen en uitvoeren met Azure container Registry taken](../container-registry/container-registry-quickstart-task-cli.md) voor meer informatie over het bouwen van installatie kopieën met een Azure container Registry

Zie [uw eerste installatie kopie naar een privé-docker-container register pushen](../container-registry/container-registry-get-started-docker-cli.md)voor meer informatie over het uploaden van bestaande installatie kopieën naar een Azure container Registry.

## <a name="use-a-custom-base-image"></a>Een aangepaste basis installatie kopie gebruiken

Als u een aangepaste installatie kopie wilt gebruiken, hebt u de volgende informatie nodig:

* De __naam van de installatie kopie__. `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest`Is bijvoorbeeld het pad naar een eenvoudige docker-installatie kopie van micro soft.

    > [!IMPORTANT]
    > Voor aangepaste installatie kopieën die u hebt gemaakt, moet u ervoor zorgen dat alle labels worden opgenomen die met de afbeelding zijn gebruikt. Als uw installatie kopie bijvoorbeeld is gemaakt met een specifieke tag, zoals `:v1` . Als u geen specifieke tag hebt gebruikt bij het maken van de installatie kopie, is een tag van `:latest` toegepast.

* Als de installatie kopie zich in een __privé opslagplaats__ bevindt, hebt u de volgende informatie nodig:

    * Het register __adres__. Bijvoorbeeld `myregistry.azureecr.io`.
    * Een __gebruikers naam__ en- __wacht woord__ voor de service-principal met lees toegang tot het REGI ster.

    Als u deze informatie niet hebt, neemt u contact op met de beheerder voor de Azure Container Registry die uw installatie kopie bevat.

### <a name="publicly-available-base-images"></a>Openbaar beschik bare basis installatie kopieën

Micro soft biedt verschillende docker-installatie kopieën op een openbaar toegankelijke opslag plaats die kan worden gebruikt met de stappen in deze sectie:

| Installatiekopie | Beschrijving |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Basis installatie kopie voor Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Bevat ONNX-runtime voor CPU-de |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Bevat de ONNX runtime en CUDA voor GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Bevat ONNX runtime en TensorRT voor GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Bevat de ONNX runtime en open-wijn voor het ontwerp van de Intel <sup></sup> Vision Accelerator op basis van Movidius<sup>TM</sup> tallozex VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Bevat ONNX runtime en-wijn voor Intel <sup></sup> Movidius<sup>TM</sup> USB-sticks |

Zie de [sectie ONNX runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) in de GitHub opslag plaats voor meer informatie over de basis installatie kopieën van ONNX-runtime.

> [!TIP]
> Omdat deze installatie kopieën openbaar beschikbaar zijn, hoeft u geen adres, gebruikers naam of wacht woord op te geven wanneer u deze gebruikt.

Zie [Azure machine learning containers](https://github.com/Azure/AzureML-Containers) opslag plaats op github voor meer informatie.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Een installatie kopie gebruiken met de Azure Machine Learning SDK

Als u een installatie kopie wilt gebruiken die is opgeslagen in de **Azure container Registry voor uw werk ruimte** of een **container register dat openbaar toegankelijk is**, stelt u de volgende [omgevings](/python/api/azureml-core/azureml.core.environment.environment) kenmerken in:

+ `docker.enabled=True`
+ `docker.base_image`: Ingesteld op het REGI ster en pad naar de installatie kopie.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Als u een installatie kopie wilt gebruiken uit een __persoonlijk container register__ dat zich niet in uw werk ruimte bevindt, moet u gebruiken `docker.base_image_registry` om het adres van de opslag plaats en een gebruikers naam en wacht woord op te geven:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

U moet de standaard waarden van azureml toevoegen met versie >= 1.0.45 als een PIP-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten. U moet ook inferencing_stack_version eigenschap op de omgeving instellen op ' meest recent ', maar Hiermee installeert u specifieke apt-pakketten die nodig zijn voor de webservice. 

Nadat u de omgeving hebt gedefinieerd, kunt u deze gebruiken met een [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) -object om de Afleidings omgeving te definiëren waarin het model en de webservice worden uitgevoerd.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

U kunt nu door gaan met de implementatie. Met het volgende code fragment wordt bijvoorbeeld lokaal een webservice geïmplementeerd met behulp van de configuratie voor inactiviteit en aangepaste installatie kopie:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over de implementatie.

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over het aanpassen van uw python-omgeving. 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Een installatie kopie gebruiken met de Machine Learning CLI

> [!IMPORTANT]
> Momenteel kan de Machine Learning CLI installatie kopieën van de Azure Container Registry gebruiken voor uw werk ruimte of openbaar toegankelijke opslag plaatsen. Het kan geen installatie kopieën van zelfstandige persoonlijke registers gebruiken.

Voordat u een model implementeert met behulp van de Machine Learning CLI, moet u een [omgeving](/python/api/azureml-core/azureml.core.environment.environment) maken die gebruikmaakt van de aangepaste installatie kopie. Maak vervolgens een Afleidings configuratie bestand dat verwijst naar de omgeving. U kunt de omgeving ook rechtstreeks in het configuratie bestand voor het dezicht opgeven. In het volgende JSON-document ziet u hoe u kunt verwijzen naar een installatie kopie in een openbaar container register. In dit voor beeld is de omgeving inline gedefinieerd:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Dit bestand wordt gebruikt met de `az ml model deploy` opdracht. De `--ic` para meter wordt gebruikt om het Afleidings configuratie bestand op te geven.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Zie de sectie model registratie, profile ring en implementatie van de [cli-uitbrei ding voor Azure machine learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artikel voor meer informatie over het implementeren van een model met de ml cli.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waar u kunt implementeren en hoe u](how-to-deploy-and-where.md)dit kunt doen.
* Leer hoe u [machine learning modellen traint en implementeert met behulp van Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning).