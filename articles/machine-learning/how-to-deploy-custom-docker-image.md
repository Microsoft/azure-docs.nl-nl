---
title: Modellen implementeren met een aangepaste Docker-afbeelding
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een aangepaste Docker-basisafbeelding om uw Azure Machine Learning implementeren. Hoewel Azure Machine Learning een standaardbasisafbeelding voor u biedt, kunt u ook uw eigen basisafbeelding gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: how-to
ms.custom: devx-track-python, deploy
ms.openlocfilehash: bd83d9f30712adf671f9a6f2dd15760c3466f175
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889714"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Een model implementeren met behulp van een aangepaste Docker-basisafbeelding

Meer informatie over het gebruik van een aangepaste Docker-basisafbeelding bij het implementeren van getrainde modellen met Azure Machine Learning.

Azure Machine Learning gebruikt een Standaard Docker-basis-docker-afbeelding als er geen is opgegeven. U vindt de specifieke Docker-afbeelding die wordt gebruikt met `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . U kunt ook een Azure Machine Learning __gebruiken om__ een specifieke basisafbeelding te selecteren of een aangepaste basisafbeelding gebruiken die u op verstrekt.

Een basisafbeelding wordt gebruikt als het beginpunt wanneer een installatiebestand wordt gemaakt voor een implementatie. Het biedt het onderliggende besturingssysteem en de onderliggende onderdelen. Tijdens het implementatieproces worden vervolgens extra onderdelen, zoals uw model, conda-omgeving en andere assets, toegevoegd aan de afbeelding.

Normaal gesproken maakt u een aangepaste basisafbeelding wanneer u Docker wilt gebruiken om uw afhankelijkheden te beheren, een betere controle over onderdeelversies te behouden of tijd te besparen tijdens de implementatie. Mogelijk wilt u ook software installeren die vereist is voor uw model, waarbij het installatieproces lang duurt. Als u de software installeert bij het maken van de basisafbeelding, hoeft u deze niet voor elke implementatie te installeren.

> [!IMPORTANT]
> Wanneer u een model implementeert, kunt u geen kernonderdelen zoals de webserver of de IoT Edge overschrijven. Deze onderdelen bieden een bekende werkomgeving die wordt getest en ondersteund door Microsoft.

> [!WARNING]
> Microsoft kan mogelijk niet helpen bij het oplossen van problemen die worden veroorzaakt door een aangepaste afbeelding. Als u problemen ondervindt, wordt u mogelijk gevraagd om de standaardafbeelding te gebruiken of een van de afbeeldingen die Microsoft verstrekt om te zien of het probleem specifiek is voor uw afbeelding.

Dit document is onderverdeeld in twee secties:

* Een aangepaste basisafbeelding maken: biedt beheerders en DevOps informatie over het maken van een aangepaste afbeelding en het configureren van verificatie voor een Azure Container Registry met behulp van de Azure CLI en Machine Learning CLI.
* Een model implementeren met behulp van een aangepaste basisafbeelding: biedt gegevens aan gegevenswetenschappers en DevOps-/ML-engineers over het gebruik van aangepaste afbeeldingen bij het implementeren van een getraind model vanuit de Python SDK of ML CLI.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie het artikel Een werkruimte maken [voor meer](how-to-manage-workspace.md) informatie.
* De [Azure Machine Learning SDK](/python/api/overview/azure/ml/install). 
* De [Azure CLI](/cli/azure/install-azure-cli).
* De [CLI-extensie voor Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Een [Azure Container Registry](../container-registry/index.yml) of een ander Docker-register dat toegankelijk is op internet.
* Bij de stappen in dit document wordt ervan uitgenomen dat u bekend bent met het maken en gebruiken van een __deferentieconfiguratieobject__ als onderdeel van de modelimplementatie. Zie Waar te implementeren en hoe [voor meer informatie.](how-to-deploy-and-where.md)

## <a name="create-a-custom-base-image"></a>Een aangepaste basisafbeelding maken

Bij de informatie in deze sectie wordt ervan uitgenomen dat u een Azure Container Registry docker-afbeeldingen op te slaan. Gebruik de volgende controlelijst bij het plannen van aangepaste afbeeldingen voor Azure Machine Learning:

* Gebruikt u de Azure Container Registry gemaakt voor de werkruimte Azure Machine Learning of een zelfstandige Azure Container Registry?

    Wanneer u afbeeldingen gebruikt die zijn opgeslagen in __het containerregister voor__ de werkruimte, hoeft u zich niet te verifiëren bij het register. Verificatie wordt afgehandeld door de werkruimte.

    > [!WARNING]
    > De Azure Container Registry voor uw werkruimte wordt __gemaakt wanneer u__ voor het eerst een model traint of implementeert met behulp van de werkruimte. Als u een nieuwe werkruimte hebt gemaakt, maar niet hebt getraind of een model hebt gemaakt, Azure Container Registry er geen werkruimte meer voor de werkruimte.

    Wanneer u afbeeldingen gebruikt die zijn opgeslagen in een zelfstandig __containerregister,__ moet u een service-principal configureren die ten minste leestoegang heeft. Vervolgens geeft u de service-principal-id (gebruikersnaam) en het wachtwoord op voor iedereen die gebruikmaakt van afbeeldingen uit het register. De uitzondering hierop is als u het containerregister openbaar toegankelijk maakt.

    Zie Een privécontainerregister maken Azure Container Registry informatie [over het maken van een privécontainerregister.](../container-registry/container-registry-get-started-azure-cli.md)

    Zie verificatie met service-principals Azure Container Registry voor Azure Container Registry het gebruik [van service-principals.](../container-registry/container-registry-auth-service-principal.md)

* Azure Container Registry en afbeeldingsgegevens: geef de naam van de afbeelding op aan iedereen die deze moet gebruiken. Er wordt bijvoorbeeld verwezen naar een installatieafbeelding met de naam , die is opgeslagen in een register met de naam , als bij het gebruik van de installatie afbeelding `myimage` `myregistry` voor `myregistry.azurecr.io/myimage` modelimplementatie

### <a name="image-requirements"></a>Vereisten voor installatiekopieën

Azure Machine Learning ondersteunt alleen Docker-afbeeldingen die de volgende software bieden:
* Ubuntu 16.04 of hoger.
* Conda 4.5.# of hoger.
* Python 3.6+.

Als u Gegevenssets wilt gebruiken, installeert u het pakket libfuse-dev. Zorg er ook voor dat u alle gebruikersruimtepakketten installeert die u mogelijk nodig hebt.

Azure ML onderhoudt een set CPU- en GPU-basisafbeeldingen die zijn gepubliceerd naar Microsoft Container Registry die u eventueel kunt gebruiken (of waarnaar wordt verwezen) in plaats van uw eigen aangepaste afbeelding te maken. Als u de Dockerfiles voor deze afbeeldingen wilt zien, raadpleegt u de [GitHub-opslagplaats Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

Voor GPU-afbeeldingen biedt Azure ML momenteel zowel cuda9- als cuda10-basisafbeeldingen. De belangrijkste afhankelijkheden die in deze basisafbeeldingen zijn geïnstalleerd, zijn:

| Afhankelijkheden | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| Mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| Cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

De CPU-installatie afbeeldingen zijn gebaseerd op ubuntu16.04. De GPU-afbeeldingen voor cuda9 zijn gebouwd vanuit nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04. De GPU-afbeeldingen voor cuda10 zijn gebouwd vanuit nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04.
<a id="getname"></a>

> [!IMPORTANT]
> Wanneer u aangepaste Docker-afbeeldingen gebruikt, is het raadzaam pakketversies vast te maken om de reproduceerbaarheid te verbeteren.

### <a name="get-container-registry-information"></a>Containerregistergegevens op halen

In deze sectie leert u hoe u de naam van de Azure Container Registry voor uw Azure Machine Learning werkruimte.

> [!WARNING]
> De Azure Container Registry voor uw werkruimte wordt __gemaakt wanneer u__ voor het eerst een model traint of implementeert met behulp van de werkruimte. Als u een nieuwe werkruimte hebt gemaakt, maar niet hebt getraind of een model hebt gemaakt, Azure Container Registry er geen werkruimte meer voor de werkruimte.

Als u al modellen hebt getraind of geïmplementeerd met behulp van Azure Machine Learning, is er een containerregister gemaakt voor uw werkruimte. Gebruik de volgende stappen om de naam van dit containerregister te vinden:

1. Open een nieuwe shell of opdrachtprompt en gebruik de volgende opdracht om te verifiëren bij uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

    Volg de aanwijzingen om te verifiëren bij het abonnement.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Gebruik de volgende opdracht om het containerregister voor de werkruimte weer te geven. Vervang `<myworkspace>` door de Azure Machine Learning werkruimtenaam. Vervang `<resourcegroup>` door de Azure-resourcegroep die uw werkruimte bevat:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    De geretourneerde informatie is vergelijkbaar met de volgende tekst:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    De `<registry_name>` waarde is de naam van de Azure Container Registry voor uw werkruimte.

### <a name="build-a-custom-base-image"></a>Een aangepaste basisafbeelding bouwen

De stappen in deze sectie gaan over het maken van een aangepaste Docker-afbeelding in uw Azure Container Registry. Zie de GitHub-opslagplaats [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) voor voorbeelden van dockerfiles.

1. Maak een nieuw tekstbestand met de `Dockerfile` naam en gebruik de volgende tekst als de inhoud:

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

2. Gebruik vanuit een shell of opdrachtprompt het volgende om te verifiëren bij de Azure Container Registry. Vervang door `<registry_name>` de naam van het containerregister waarin u de afbeelding wilt opslaan:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Gebruik de volgende opdracht om het Dockerfile te uploaden en te bouwen. Vervang `<registry_name>` door de naam van het containerregister waarin u de afbeelding wilt opslaan:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > In dit voorbeeld wordt een tag van `:v1` toegepast op de afbeelding. Als er geen tag is opgegeven, wordt er een tag van `:latest` toegepast.

    Tijdens het bouwproces wordt informatie naar de opdrachtregel gestreamd. Als de build is geslaagd, ontvangt u een bericht dat lijkt op de volgende tekst:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Zie Een [containerafbeelding](../container-registry/container-registry-quickstart-task-cli.md) bouwen en uitvoeren met behulp van een Azure Container Registry voor meer informatie over het bouwen van afbeeldingen met Azure Container Registry-taken

Zie Push your first image to a private Docker container registry (Uw eerste Azure Container Registry naar een [privé-Docker-containerregister pushen)](../container-registry/container-registry-get-started-docker-cli.md)voor meer informatie over het uploaden van bestaande afbeeldingen naar een Azure Container Registry.

## <a name="use-a-custom-base-image"></a>Een aangepaste basisafbeelding gebruiken

Als u een aangepaste afbeelding wilt gebruiken, hebt u de volgende informatie nodig:

* De __naam van de afbeelding.__ Is bijvoorbeeld het `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` pad naar een eenvoudige Docker-afbeelding die door Microsoft wordt geleverd.

    > [!IMPORTANT]
    > Voor aangepaste afbeeldingen die u hebt gemaakt, moet u tags opnemen die zijn gebruikt met de afbeelding. Bijvoorbeeld als uw afbeelding is gemaakt met een specifieke tag, zoals `:v1` . Als u geen specifieke tag hebt gebruikt bij het maken van de afbeelding, is er een tag van `:latest` toegepast.

* Als de afbeelding zich in een __privéopslagplaats ,__ hebt u de volgende informatie nodig:

    * Het __registeradres__. Bijvoorbeeld `myregistry.azureecr.io`.
    * Een gebruikersnaam en __wachtwoord voor__ __een service-principal__ die leestoegang tot het register hebben.

    Als u deze informatie niet hebt, spreekt u met de beheerder voor de Azure Container Registry uw afbeelding bevat.

### <a name="publicly-available-base-images"></a>Openbaar beschikbare basisafbeeldingen

Microsoft biedt verschillende Docker-afbeeldingen in een openbaar toegankelijke opslagplaats, die kunnen worden gebruikt met de stappen in deze sectie:

| Installatiekopie | Description |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Basisafbeelding voor Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Bevat ONNX Runtime voor CPU-deferencing |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Bevat de ONNX Runtime en CUDA voor GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Bevat ONNX Runtime en TensorRT voor GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Bevat ONNX Runtime en OpenVINO voor Intel <sup></sup> Vision Accelerator Design op basis van Movidius TM MyriadX<sup>VPUs</sup> |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Bevat ONNX Runtime en OpenVINO voor Intel <sup></sup> Movidius<sup>TM</sup> USB-sticks |

Zie de sectie [ONNX Runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) in de GitHub-opslagplaats voor meer informatie over de ONNX Runtime-basisafbeeldingen.

> [!TIP]
> Omdat deze afbeeldingen openbaar beschikbaar zijn, hoeft u geen adres, gebruikersnaam of wachtwoord op te geven wanneer u ze gebruikt.

Zie de opslagplaats Azure Machine Learning [containers op](https://github.com/Azure/AzureML-Containers) GitHub voor meer informatie.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Een afbeelding gebruiken met de Azure Machine Learning SDK

Als u een afbeelding wilt gebruiken die is opgeslagen in de Azure Container Registry voor uw werkruimte **of** een containerregister dat openbaar toegankelijk **is,** stelt u de volgende omgevingskenmerken [](/python/api/azureml-core/azureml.core.environment.environment) in:

+ `docker.enabled=True`
+ `docker.base_image`: stel in op het register en het pad naar de afbeelding.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Als u een afbeelding wilt gebruiken uit een __privécontainerregister__ dat zich niet in uw werkruimte, moet u gebruiken om het adres van de opslagplaats en een gebruikersnaam en wachtwoord `docker.base_image_registry` op te geven:

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

U moet azureml-defaults toevoegen met versie >= 1.0.45 als pip-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als webservice te hosten. U moet de eigenschap inferencing_stack_version de omgeving ook instellen op 'meest recente'. Hiermee worden specifieke apt-pakketten geïnstalleerd die nodig zijn voor de webservice. 

Nadat u de omgeving heeft definiëren, gebruikt u deze met een [InferenceConfig-object](/python/api/azureml-core/azureml.core.model.inferenceconfig) om deference-omgeving te definiëren waarin het model en de webservice worden uitgevoerd.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Op dit moment kunt u doorgaan met de implementatie. Met het volgende codefragment wordt bijvoorbeeld een webservice lokaal geïmplementeerd met behulp van de deference-configuratie en de aangepaste installatie afbeelding:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie Modellen implementeren met Azure Machine Learning voor [meer informatie over Azure Machine Learning.](how-to-deploy-and-where.md)

Zie Omgevingen maken en beheren voor training en implementatie voor meer informatie over het aanpassen van [uw Python-omgeving.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Een afbeelding gebruiken met de Machine Learning CLI

> [!IMPORTANT]
> Momenteel kan Machine Learning CLI gebruikmaken van afbeeldingen uit de Azure Container Registry voor uw werkruimte of openbaar toegankelijke opslagplaatsen. Er kunnen geen afbeeldingen uit zelfstandige privéregisters worden gebruikt.

Voordat u een model implementeert met behulp van Machine Learning CLI, maakt u een [omgeving](/python/api/azureml-core/azureml.core.environment.environment) die gebruikmaakt van de aangepaste afbeelding. Maak vervolgens een deferentieconfiguratiebestand dat verwijst naar de omgeving. U kunt de omgeving ook rechtstreeks in het deference-configuratiebestand definiëren. In het volgende JSON-document wordt gedemonstreerd hoe u verwijst naar een afbeelding in een openbaar containerregister. In dit voorbeeld wordt de omgeving inline gedefinieerd:

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

Dit bestand wordt gebruikt met de `az ml model deploy` opdracht . De `--ic` parameter wordt gebruikt om het configuratiebestand voor de deference op te geven.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Zie de sectie 'modelregistratie, profilering en implementatie' van de CLI-extensie voor Azure Machine Learning voor meer informatie over het implementeren van een model met behulp van de ML [CLI.](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waar te implementeren en hoe](how-to-deploy-and-where.md).
* Meer informatie over het [trainen en implementeren machine learning modellen met behulp van Azure Pipelines.](/azure/devops/pipelines/targets/azure-machine-learning)