---
title: Azure Machine Learning CLI installeren & gebruiken
description: Meer informatie over hoe u de Azure CLI-extensie voor ML kunt gebruiken om resources te maken & te beheren, zoals uw werk ruimte, gegevens opslag, gegevens sets, pijp lijnen, modellen en implementaties.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: ee10866791d1e5015ef5c91d98bff286ed6ce67a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591902"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Installeer & gebruik de CLI-extensie voor Azure Machine Learning


De Azure Machine Learning CLI is een uitbrei ding van [Azure cli](/cli/azure/?preserve-view=true&view=azure-cli-latest), een platformoverschrijdende opdracht regel interface voor het Azure-platform. Deze uitbrei ding bevat opdrachten voor het werken met Azure Machine Learning. Zo kunt u uw machine learning activiteiten automatiseren. De volgende lijst bevat enkele voor beelden van acties die u kunt uitvoeren met de CLI-extensie:

+ Experimenten uitvoeren om machine learning modellen te maken

+ machine learning modellen registreren voor klant gebruik

+ De levens cyclus van uw machine learning modellen inpakken, implementeren en bijhouden

De CLI is geen vervanging voor de Azure Machine Learning SDK. Het is een aanvullend hulp programma dat is geoptimaliseerd voor het verwerken van zeer geparametriseerde taken die goed zijn afgestemd op automatisering.

## <a name="prerequisites"></a>Vereisten

* Als u de CLI wilt gebruiken, moet u een Azure-abonnement hebben. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

    Als u [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) gebruikt, opent u de CLI via de browser en bevindt deze zich in de cloud.

## <a name="full-reference-docs"></a>Volledige referentie documenten

Zoek de [volledige referentie documenten voor de extensie Azure-cli-ml van Azure cli](/cli/azure/ext/azure-cli-ml/?preserve-view=true&view=azure-cli-latest).

## <a name="connect-the-cli-to-your-azure-subscription"></a>De CLI koppelen aan uw Azure-abonnement

> [!IMPORTANT]
> Als u de Azure Cloud Shell gebruikt, kunt u deze sectie overs Laan. De Cloud shell verifieert u automatisch met het account dat u hebt aangemeld bij uw Azure-abonnement.

Er zijn verschillende manieren waarop u zich kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De eenvoudigste methode is interactieve verificatie met behulp van een browser. Voor interactieve verificatie opent u een opdrachtregel of terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de aanwijzingen op de opdrachtregel volgen. Dit omvat het bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en het invoeren van een autorisatiecode.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Zie [Aanmelden met Azure cli](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest)voor andere verificatie methoden.

## <a name="install-the-extension"></a>De extensie installeren

Als u de Machine Learning CLI-extensie wilt installeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Voorbeeld bestanden die u kunt gebruiken met de onderstaande opdrachten, vindt u [hier](https://aka.ms/azml-deploy-cloud).

Wanneer u hierom wordt gevraagd, selecteert `y` u om de uitbrei ding te installeren.

Als u wilt controleren of de extensie is geïnstalleerd, gebruikt u de volgende opdracht om een lijst met ML-specifieke subopdrachten weer te geven:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>De extensie bijwerken

Als u de Machine Learning CLI-extensie wilt bijwerken, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>De extensie verwijderen

Als u de CLI-extensie wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resourcebeheer

De volgende opdrachten laten zien hoe u de CLI gebruikt om resources te beheren die worden gebruikt door Azure Machine Learning.

+ Als u er nog geen hebt, maakt u een resource groep:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Een Azure Machine Learning-werk ruimte maken:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Zie [AZ ml Workspace Create](/cli/azure/ext/azure-cli-ml/ml/workspace?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)(Engelstalig) voor meer informatie.

+ Voeg een werkruimte configuratie toe aan een map om CLI-contextuele bewustzijn in te scha kelen.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Met deze opdracht maakt u een `.azureml` submap die voor beelden van runconfig-en Conda-omgevings bestanden bevat. Het bevat ook een `config.json` bestand dat wordt gebruikt om te communiceren met uw Azure machine learning-werk ruimte.

    Zie voor meer informatie [AZ ml map attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Een Azure Blob-container als gegevens opslag koppelen.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Zie voor meer informatie [AZ ml Data Store attach-BLOB](/cli/azure/ext/azure-cli-ml/ml/datastore?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Bestanden uploaden naar een gegevens opslag.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Zie voor meer informatie [AZ ml Data Store upload](/cli/azure/ext/azure-cli-ml/ml/datastore?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Koppel een AKS-cluster als een compute-doel.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Zie [AZ ml computetarget attach AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) voor meer informatie.

### <a name="compute-clusters"></a>Reken clusters

+ Maak een nieuw beheerd reken cluster.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Een nieuw beheerd reken cluster maken met beheerde identiteit

  + Door de gebruiker toegewezen beheerde identiteit

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Door het systeem toegewezen beheerde identiteit

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Een beheerde identiteit toevoegen aan een bestaand cluster:

    + Door de gebruiker toegewezen beheerde identiteit
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Door het systeem toegewezen beheerde identiteit

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Zie [AZ ml computetarget Create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)voor meer informatie.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Rekenproces
Reken instanties beheren.  In alle onderstaande voor beelden is de naam van de reken instantie **CPU**

+ Maak een nieuwe computeinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Zie [AZ ml computetarget Create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance)voor meer informatie.

+ Een computeinstance stoppen.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Zie [AZ ml computetarget computeinstance stop](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)voor meer informatie.

+ Een computeinstance starten.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Zie [AZ ml computetarget computeinstance start](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)voor meer informatie.

+ Start een computeinstance opnieuw.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Zie [AZ ml computetarget computeinstance restart](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart)voor meer informatie.

+ Een computeinstance verwijderen.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Zie [AZ ml computetarget delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-delete)voor meer informatie.


## <a name="run-experiments"></a><a id="experiments"></a>Experimenten uitvoeren

* Start een uitvoering van uw experiment. Wanneer u deze opdracht gebruikt, geeft u de naam op van het runconfig-bestand (de tekst voor \* . runconfig als u uw bestands systeem bekijkt) op basis van de para meter-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`Met de opdracht wordt een `.azureml` submap gemaakt die twee voor beelden van runconfig-bestanden bevat. 
    >
    > Als u een python-script hebt waarmee een configuratie object voor een uitvoering programmatisch wordt gemaakt, kunt u [RunConfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als een RunConfig-bestand.
    >
    > Het volledige runconfig-schema vindt u in dit [JSON-bestand](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Het schema wordt zelf gedocumenteerd via de `description` sleutel van elk object. Daarnaast zijn er opsommingen voor mogelijke waarden en een sjabloon fragment aan het einde.

    Zie [AZ ml run-script uitvoeren](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)voor meer informatie.

* Een lijst met experimenten weer geven:

    ```azurecli-interactive
    az ml experiment list
    ```

    Zie voor meer informatie [AZ ml experimenten List](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

### <a name="hyperdrive-run"></a>HyperDrive uitvoeren

U kunt HyperDrive gebruiken met Azure CLI om het afstemmen van de para meters uit te voeren. Maak eerst een HyperDrive-configuratie bestand in de volgende indeling. Zie [Hyper parameters afstemmen voor uw model](how-to-tune-hyperparameters.md) artikel voor meer informatie over afstemming-tuning-para meters.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Voeg dit bestand toe naast de configuratie bestanden voor uitvoeren. Dien vervolgens een HyperDrive-uitvoering in met:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Let op de sectie *argumenten* in runconfig en de *parameter ruimte* in HyperDrive config. Ze bevatten de opdracht regel argumenten die moeten worden door gegeven aan het trainings script. De waarde in runconfig blijft hetzelfde voor elke iteratie, terwijl het bereik in HyperDrive config wordt herhaald. Geef in beide bestanden niet hetzelfde argument op.

## <a name="dataset-management"></a>Beheer van gegevensset

De volgende opdrachten laten zien hoe u met gegevens sets in Azure Machine Learning kunt werken:

+ Een gegevensset registreren:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Voor informatie over de indeling van het JSON-bestand dat wordt gebruikt voor het definiëren van de gegevensset, gebruikt u `az ml dataset register --show-template` .

    Zie voor meer informatie [AZ ml dataset REGI ster](/cli/azure/ext/azure-cli-ml/ml/dataset?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-register).

+ Alle gegevens sets in een werk ruimte weer geven:

    ```azurecli-interactive
    az ml dataset list
    ```

    Zie de [lijst AZ ml dataset](/cli/azure/ext/azure-cli-ml/ml/dataset?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-list)voor meer informatie.

+ Details van een gegevensset ophalen:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Zie [AZ ml dataset show](/cli/azure/ext/azure-cli-ml/ml/dataset?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-show)(Engelstalig) voor meer informatie.

+ Registratie van een gegevensset opheffen:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Zie [AZ ml dataset unregisterd](/cli/azure/ext/azure-cli-ml/ml/dataset?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)(Engelstalig) voor meer informatie.

## <a name="environment-management"></a>Omgevingsbeheer

De volgende opdrachten laten zien hoe u Azure Machine Learning [omgevingen](how-to-configure-environment.md) kunt maken, registreren en weer geven voor uw werk ruimte:

+ Maak een steiger bestand voor een omgeving:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Zie [AZ ml Environment steigers](/cli/azure/ext/azure-cli-ml/ml/environment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)voor meer informatie.

+ Een omgeving registreren:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Zie [AZ ml Environment REGI ster](/cli/azure/ext/azure-cli-ml/ml/environment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)(Engelstalig) voor meer informatie.

+ Geregistreerde omgevingen weer geven:

    ```azurecli-interactive
    az ml environment list
    ```

    Zie de [lijst AZ ml Environment](/cli/azure/ext/azure-cli-ml/ml/environment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)(Engelstalig) voor meer informatie.

+ Een geregistreerde omgeving downloaden:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Zie [AZ ml Environment down load](/cli/azure/ext/azure-cli-ml/ml/environment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)(Engelstalig) voor meer informatie.

### <a name="environment-configuration-schema"></a>Configuratie schema omgeving

Als u de opdracht hebt gebruikt, wordt er `az ml environment scaffold` een sjabloon `azureml_environment.json` bestand gegenereerd dat kan worden gewijzigd en gebruikt om aangepaste omgevings configuraties te maken met de cli. Het object op het hoogste niveau is in de python-SDK los toegewezen aan de [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) klasse. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

De volgende tabel bevat informatie over elk veld op het hoogste niveau in het JSON-bestand, het type en een beschrijving. Als een object type is gekoppeld aan een klasse uit de python-SDK, is er een losse 1:1 overeenkomst tussen elk JSON-veld en de naam van de open bare variabele in de python-klasse. In sommige gevallen kan het veld worden toegewezen aan een constructor-argument in plaats van een klassen variabele. Het veld wordt bijvoorbeeld `environmentVariables` toegewezen aan de `environment_variables` variabele in de- [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) klasse.

| JSON-veld | Type | Description |
|---|---|---|
| `name` | `string` | De naam van de omgeving. Start de naam niet met **micro soft** of **AzureML**. |
| `version` | `string` | De versie van de omgeving. |
| `environmentVariables` | `{string: string}` | Een hash-toewijzing van namen en waarden van omgevings variabelen. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection?preserve-view=true&view=azure-ml-py)Hat definieert de python-omgeving en-interpreter die moeten worden gebruikt voor de doel Compute-resource. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection?preserve-view=true&view=azure-ml-py) | Definieert instellingen voor het aanpassen van de docker-installatie kopie die is gebaseerd op de specificaties van de omgeving. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection?preserve-view=true&view=azure-ml-py) | In het gedeelte worden Spark-instellingen geconfigureerd. Het wordt alleen gebruikt wanneer Framework is ingesteld op PySpark. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection?preserve-view=true&view=azure-ml-py) | Hiermee configureert u Databricks-bibliotheek afhankelijkheden. |
| `inferencingStackVersion` | `string` | Hiermee geeft u de versie van de stack voor het in de wachtrij plaatsen toevoegen aan de installatie kopie. Als u wilt voor komen dat een inleidende stack wordt toegevoegd, verlaat u dit veld `null` . Geldige waarde: ' meest recent '. |

## <a name="ml-pipeline-management"></a>ML pijplijn beheer

De volgende opdrachten laten zien hoe u met machine learning pijp lijnen kunt werken:

+ Een machine learning-pijp lijn maken:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Zie voor meer informatie [AZ ml pijp lijn Create](/cli/azure/ext/azure-cli-ml/ml/pipeline?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Zie [machine learning-pijp lijnen definiëren in YAML](reference-pipeline-yaml.md)voor meer informatie over het yaml-bestand van de pijp lijn.

+ Een pijp lijn uitvoeren:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Zie voor meer informatie [AZ ml run-pipeline](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Zie [machine learning-pijp lijnen definiëren in YAML](reference-pipeline-yaml.md)voor meer informatie over het yaml-bestand van de pijp lijn.

+ Een pijp lijn plannen:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Zie voor meer informatie [AZ ml pijp lijn Create-Schedule](/cli/azure/ext/azure-cli-ml/ml/pipeline?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Zie [machine learning-pijp lijnen definiëren in YAML](reference-pipeline-yaml.md#schedules)voor meer informatie over het yaml-bestand van de pipeline-planning.

## <a name="model-registration-profiling-deployment"></a>Model registratie, profile ring, implementatie

De volgende opdrachten laten zien hoe u een getraind model kunt registreren en vervolgens als een productie service kunt implementeren:

+ Een model registreren bij Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Zie voor meer informatie [AZ ml model REGI ster](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **Optioneel** Profiel uw model om optimale CPU-en geheugen waarden voor implementatie te verkrijgen.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Zie [AZ ml model profile](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)(Engelstalig) voor meer informatie.

+ Implementeer uw model op AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Zie voor meer informatie over het schema voor het dezicht van de configuratie voor het dezicht configuratie [schema](#inferenceconfig)voor het inlichten.
    
    Zie [implementatie configuratie schema](#deploymentconfig)voor meer informatie over het schema van het configuratie bestand voor implementatie.

    Zie [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)(Engelstalig) voor meer informatie.

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Configuratie schema voor afleiding

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Configuratie schema implementatie

### <a name="local-deployment-configuration-schema"></a>Configuratie schema voor lokale implementatie

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Configuratie schema voor implementatie van Azure container instance 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Configuratie schema voor implementatie van Azure Kubernetes service

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Volgende stappen

* [Opdracht verwijzing voor de machine learning cli-extensie](/cli/azure/ext/azure-cli-ml/ml?preserve-view=true&view=azure-cli-latest).

* [machine learning modellen trainen en implementeren met behulp van Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning)
