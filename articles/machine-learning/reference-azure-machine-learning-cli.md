---
title: Gebruik & CLI Azure Machine Learning installeren
description: Leer hoe u de Azure CLI-extensie voor ML kunt gebruiken om & resources te maken, zoals uw werkruimte, gegevensstores, gegevenssets, pijplijnen, modellen en implementaties.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 04/02/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f30cd528a329708a7478b1a4a343f7be3b9eac04
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877782"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Installeer & CLI-extensie voor Azure Machine Learning


De Azure Machine Learning CLI is een uitbreiding op [de Azure CLI,](/cli/azure/)een platformoverschrijdende opdrachtregelinterface voor het Azure-platform. Deze extensie biedt opdrachten voor het werken met Azure Machine Learning. Hiermee kunt u uw machine learning automatiseren. De volgende lijst bevat enkele voorbeeldacties die u met de CLI-extensie kunt uitvoeren:

+ Experimenten uitvoeren om machine learning maken

+ De machine learning registreren voor klantgebruik

+ De levenscyclus van uw machine learning verpakken, implementeren machine learning volgen

De CLI is geen vervanging voor de Azure Machine Learning SDK. Het is een aanvullend hulpprogramma dat is geoptimaliseerd voor het afhandelen van zeer geparameteriseerde taken die geschikt zijn voor automatisering.

## <a name="prerequisites"></a>Vereisten

* Als u de CLI wilt gebruiken, moet u een Azure-abonnement hebben. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](/cli/azure/install-azure-cli).

    Als u [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) gebruikt, opent u de CLI via de browser en bevindt deze zich in de cloud.

## <a name="full-reference-docs"></a>Volledige referentie docs

Zoek de [volledige referentie docs voor de azure-cli-ml-extensie van Azure CLI](/cli/azure/ml/).

## <a name="connect-the-cli-to-your-azure-subscription"></a>De CLI verbinden met uw Azure-abonnement

> [!IMPORTANT]
> Als u de Azure Cloud Shell gebruikt, kunt u deze sectie overslaan. De Cloud Shell verifieert u automatisch met behulp van het account dat u bij uw Azure-abonnement aanmeldt.

Er zijn verschillende manieren waarop u zich kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De eenvoudigste methode is interactieve verificatie met behulp van een browser. Voor interactieve verificatie opent u een opdrachtregel of terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de aanwijzingen op de opdrachtregel volgen. Dit omvat het bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en het invoeren van een autorisatiecode.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Zie Aanmelden met Azure CLI voor andere methoden [voor authenticatie.](/cli/azure/authenticate-azure-cli)

## <a name="install-the-extension"></a>De extensie installeren

De extensie wordt automatisch geïnstalleerd wanneer u voor het eerst een opdracht probeert te gebruiken die begint met `az ml` .

## <a name="update-the-extension"></a>De extensie bijwerken

Gebruik de volgende Machine Learning cli-extensie bij te werken:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="remove-the-extension"></a>De extensie verwijderen

Gebruik de volgende opdracht om de CLI-extensie te verwijderen:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resourcebeheer

De volgende opdrachten laten zien hoe u de CLI gebruikt voor het beheren van resources die worden gebruikt door Azure Machine Learning.

+ Als u er nog geen hebt, maakt u een resourcegroep:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Maak een Azure Machine Learning werkruimte:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Zie az [ml workspace create voor meer informatie.](/cli/azure/ml/workspace#az_ml_workspace_create)

+ Koppel een werkruimteconfiguratie aan een map om contextuele kennis van CLI mogelijk te maken.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Met deze opdracht maakt u `.azureml` een subdirectory met voorbeeld runconfig- en conda-omgevingsbestanden. Het bevat ook een `config.json` bestand dat wordt gebruikt om te communiceren met uw Azure Machine Learning werkruimte.

    Zie az [ml folder attach voor meer informatie.](/cli/azure/ml/folder#az_ml_folder_attach)

+ Koppel een Azure Blob-container als een gegevensopslag.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Zie az [ml datastore attach-blob voor meer informatie.](/cli/azure/ml/datastore#az_ml_datastore_attach-blob)

+ Bestanden uploaden naar een gegevensstore.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Zie az [ml datastore upload voor meer informatie.](/cli/azure/ml/datastore#az_ml_datastore_upload)

+ Koppel een AKS-cluster als een rekendoel.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Zie az [ml computetarget attach aks](/cli/azure/ml/computetarget/attach#az_ml_computetarget_attach-aks) voor meer informatie

### <a name="compute-clusters"></a>Rekenclusters

+ Maak een nieuw beheerd rekencluster.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Een nieuw beheerd rekencluster maken met een beheerde identiteit

  + Door de gebruiker toegewezen beheerde identiteit

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Door het systeem toegewezen beheerde identiteit

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Voeg een beheerde identiteit toe aan een bestaand cluster:

    + Door de gebruiker toegewezen beheerde identiteit
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Door het systeem toegewezen beheerde identiteit

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Zie az [ml computetarget create amlcompute voor meer informatie.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Rekenproces
Reken-exemplaren beheren.  In alle onderstaande voorbeelden is de naam van het reken-exemplaar **cpu**

+ Maak een nieuwe berekeningsinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Zie az [ml computetarget create computeinstance voor meer informatie.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance)

+ Een rekeninstantie stoppen.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Zie az [ml computetarget computeinstance stop voor meer informatie.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop)

+ Start een berekeningsinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Zie az [ml computetarget computeinstance start voor meer informatie.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start)

+ Start een rekeninstance opnieuw op.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Zie az [ml computetarget computeinstance restart voor meer informatie.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart)

+ Een rekeninstantie verwijderen.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Zie az [ml computetarget delete computeinstance voor meer informatie.](/cli/azure/ml/computetarget#az_ml_computetarget_delete)


## <a name="run-experiments"></a><a id="experiments"></a>Experimenten uitvoeren

* Start een run van uw experiment. Wanneer u deze opdracht gebruikt, geeft u de naam op van het runconfig-bestand (de tekst vóór .runconfig als u naar uw bestandssysteem kijkt) op basis van de \* parameter -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Met `az ml folder attach` de opdracht maakt u een `.azureml` subdirectory, die twee voorbeeldrunconfig-bestanden bevat. 
    >
    > Als u een Python-script hebt dat programmatisch een run configuration-object maakt, kunt u [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als een runconfig-bestand.
    >
    > Het volledige runconfig-schema vindt u in dit [JSON-bestand](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Het schema documenteert zichzelf via de `description` sleutel van elk object. Daarnaast zijn er enums voor mogelijke waarden en een sjabloonfragment aan het einde.

    Zie az [ml run submit-script voor meer informatie.](/cli/azure/ml/run#az_ml_run_submit_script)

* Bekijk een lijst met experimenten:

    ```azurecli-interactive
    az ml experiment list
    ```

    Zie az [ml experiment list voor meer informatie.](/cli/azure/ml/experiment#az_ml_experiment_list)

### <a name="hyperdrive-run"></a>HyperDrive uitvoeren

U kunt HyperDrive met Azure CLI gebruiken om uitvoeringen voor het afstemmen van parameters uit te voeren. Maak eerst een HyperDrive-configuratiebestand in de volgende indeling. Zie [hyperparameters afstemmen voor uw modelartikel](how-to-tune-hyperparameters.md) voor meer informatie over de afstemmingsparameterparameters van hyperparameters.

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

Voeg dit bestand toe naast de run-configuratiebestanden. Verzend vervolgens een HyperDrive-run met behulp van:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Let op *de sectie argumenten* in runconfig en *parameterruimte* in HyperDrive-configuratie. Ze bevatten de opdrachtregelargumenten die moeten worden doorgegeven aan het trainingsscript. De waarde in runconfig blijft voor elke iteratie hetzelfde, terwijl het bereik in de HyperDrive-configuratie wordt iterated. Geef niet hetzelfde argument op in beide bestanden.

## <a name="dataset-management"></a>Gegevenssetbeheer

De volgende opdrachten laten zien hoe u met gegevenssets in uw Azure Machine Learning:

+ Een gegevensset registreren:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Gebruik voor informatie over de indeling van het JSON-bestand dat wordt gebruikt om de gegevensset te `az ml dataset register --show-template` definiëren.

    Zie az [ml dataset register voor meer informatie.](/cli/azure/ml/dataset#az_ml_dataset_register)

+ Een lijst met alle gegevenssets in een werkruimte maken:

    ```azurecli-interactive
    az ml dataset list
    ```

    Zie az [ml dataset list voor meer informatie.](/cli/azure/ml/dataset#az_ml_dataset_list)

+ Details van een gegevensset op te halen:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Zie az [ml dataset show voor meer informatie.](/cli/azure/ml/dataset#az_ml_dataset_show)

+ Registratie van een gegevensset ongedaan maken:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Zie az [ml dataset unregister](/cli/azure/ml/dataset#az_ml_dataset_archive)voor meer informatie.

## <a name="environment-management"></a>Omgevingsbeheer

De volgende opdrachten laten zien hoe u een lijst met omgevingen voor Azure Machine Learning werkruimte [maakt,](how-to-configure-environment.md) registreert en vermeldt:

+ Maak scaffolding-bestanden voor een omgeving:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Zie az [ml environment scaffold voor meer informatie.](/cli/azure/ml/environment#az_ml_environment_scaffold)

+ Een omgeving registreren:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Zie az [ml environment register voor meer informatie.](/cli/azure/ml/environment#az_ml_environment_register)

+ Lijst met geregistreerde omgevingen:

    ```azurecli-interactive
    az ml environment list
    ```

    Zie az [ml environment list voor meer informatie.](/cli/azure/ml/environment#az_ml_environment_list)

+ Een geregistreerde omgeving downloaden:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Zie az [ml environment download voor meer informatie.](/cli/azure/ml/environment#az_ml_environment_download)

### <a name="environment-configuration-schema"></a>Omgevingsconfiguratieschema

Als u de opdracht hebt gebruikt, wordt er een sjabloonbestand gegenereerd dat kan worden gewijzigd en gebruikt om aangepaste `az ml environment scaffold` `azureml_environment.json` omgevingsconfiguraties te maken met de CLI. Het object op het hoogste niveau wordt losjes aan [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) de klasse in de Python-SDK toegelaten. 

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

De volgende tabel bevat elk veld op het hoogste niveau in het JSON-bestand, het type en een beschrijving. Als een objecttype is gekoppeld aan een klasse van de Python SDK, is er een losse 1:1-overeenkomst tussen elk JSON-veld en de naam van de openbare variabele in de Python-klasse. In sommige gevallen kan het veld worden toe te staan aan een constructorargument in plaats van een klassevariabele. Het veld wordt bijvoorbeeld `environmentVariables` aan de variabele in de klasse toe te `environment_variables` [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) staan.

| JSON-veld | Type | Description |
|---|---|---|
| `name` | `string` | Naam van de omgeving. Begin de naam niet met **Microsoft** of **AzureML.** |
| `version` | `string` | Versie van de omgeving. |
| `environmentVariables` | `{string: string}` | Een hash-kaart van namen en waarden van omgevingsvariabelen. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)hat definieert de Python-omgeving en interpreter voor gebruik op de doelrekenresource. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | Hiermee definieert u instellingen voor het aanpassen van de Docker-afbeelding die is gebouwd op de specificaties van de omgeving. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | In de sectie worden Spark-instellingen geconfigureerd. Het wordt alleen gebruikt wanneer framework is ingesteld op PySpark. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Hiermee configureert u Databricks-bibliotheekafhankelijkheden. |
| `inferencingStackVersion` | `string` | Hiermee geeft u de versie van de deferencing stack toegevoegd aan de afbeelding. Laat dit veld staan om te voorkomen dat u een deferencingstack `null` toevoegt. Geldige waarde: 'meest recente'. |

## <a name="ml-pipeline-management"></a>ML-pijplijnbeheer

De volgende opdrachten laten zien hoe u werkt met machine learning pijplijnen:

+ Maak een machine learning pijplijn:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Zie az [ml pipeline create voor meer informatie.](/cli/azure/ml/pipeline#az_ml_pipeline_create)

    Zie Define machine learning pipelines in YAML voor meer informatie over het [YAML-bestand van de pijplijn.](reference-pipeline-yaml.md)

+ Een pijplijn uitvoeren:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Zie az [ml run submit-pipeline voor meer informatie.](/cli/azure/ml/run#az_ml_run_submit_pipeline)

    Zie Define [machine learning pipelines in YAML (Pijplijnen definiëren in YAML) voor meer informatie over het YAML-pijplijnbestand.](reference-pipeline-yaml.md)

+ Een pijplijn plannen:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Zie az [ml pipeline create-schedule voor meer informatie.](/cli/azure/ml/pipeline#az_ml_pipeline_create-schedule)

    Zie Define machine learning pipelines in YAML (Pijplijnen definiëren in YAML) machine learning informatie over het [YAML-bestand voor pijplijnen.](reference-pipeline-yaml.md#schedules)

## <a name="model-registration-profiling-deployment"></a>Modelregistratie, profilering, implementatie

De volgende opdrachten laten zien hoe u een getraind model registreert en vervolgens implementeert als een productieservice:

+ Een model registreren bij Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Zie az [ml model register voor meer informatie.](/cli/azure/ml/model#az_ml_model_register)

+ **OPTIONEEL** Profileer uw model voor optimale CPU- en geheugenwaarden voor implementatie.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Zie az [ml model profile voor meer informatie.](/cli/azure/ml/model#az_ml_model_profile)

+ Uw model implementeren in AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Zie Configuratieschema voor de deferentie voor meer informatie over het schema [van het deferentieconfiguratiebestand.](#inferenceconfig)
    
    Zie Implementatieconfiguratieschema voor meer informatie over het schema van het [implementatieconfiguratiebestand.](#deploymentconfig)

    Zie az [ml model deploy voor meer informatie.](/cli/azure/ml/model#az_ml_model_deploy)

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Configuratieschema voor de deferentie

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Implementatieconfiguratieschema

### <a name="local-deployment-configuration-schema"></a>Configuratieschema voor lokale implementatie

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Configuratieschema voor implementatie van Azure Container Instance 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service implementatieconfiguratieschema configureren

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Volgende stappen

* [Opdrachtverwijzing voor de cli Machine Learning extensie](/cli/azure/ml).

* [Modellen trainen en machine learning implementeren met behulp van Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
