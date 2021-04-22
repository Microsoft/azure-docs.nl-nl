---
title: Het implementeren van machine learning modellen
titleSuffix: Azure Machine Learning
description: Leer hoe en waar u uw machine learning implementeert. Implementeer Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge en FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2
adobe-target: true
ms.openlocfilehash: f2128949090ce0ec2aa4ed66eb476384d662953a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872637"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Uw machine learning implementeren in Azure

Meer informatie over het implementeren van machine learning of deep learning-model als een webservice in de Azure-cloud. U kunt ook implementeren op Azure IoT Edge apparaten.

De werkstroom is vrijwel altijd hetzelfde, ongeacht waar u het model implementeert:

1. Registreer het model (optioneel, zie hieronder).
1. Bereid een deferentieconfiguratie voor (tenzij u [implementatie zonder code gebruikt).](./how-to-deploy-no-code-deployment.md)
1. Bereid een invoerscript voor (tenzij u [implementatie zonder code gebruikt).](./how-to-deploy-no-code-deployment.md)
1. Kies een rekendoel.
1. Implementeer het model op het rekendoel.
1. Test de resulterende webservice.

Zie [Manage, deploy, and monitor models with](concept-model-management-and-deployment.md)Azure Machine Learning (Modellen beheren, implementeren en bewaken met Azure Machine Learning) voor meer informatie over de concepten machine learning implementatiewerkstroom.

## <a name="prerequisites"></a>Vereisten

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)
- Een model. Als u geen getraind model hebt, kunt u het model en de afhankelijkheidsbestanden in deze [zelfstudie gebruiken.](https://aka.ms/azml-deploy-cloud)
- De [Azure CLI-extensie (Command Line Interface) voor de Machine Learning service](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)
- Een model. Als u geen getraind model hebt, kunt u het model en de afhankelijkheidsbestanden in deze [zelfstudie gebruiken.](https://aka.ms/azml-deploy-cloud)
- De [Azure Machine Learning Software Development Kit (SDK) voor Python.](/python/api/overview/azure/ml/intro)

---

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werkruimte

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Volg de instructies in de Azure CLI-documentatie voor het [instellen van uw abonnementscontext.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

Doe vervolgens het volgende:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

om te zien tot welke werkruimten u toegang hebt.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Zie de documentatie over de SDK voor Python voor meer informatie over het gebruik van [de SDK](/python/api/overview/azure/ml/intro#workspace) om verbinding te Azure Machine Learning maken met een werkruimte.


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Uw model registreren (optioneel)

Een geregistreerd model is een logische container voor een of meer bestanden waar uw model uit bestaat. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in de werkruimte. Nadat u de bestanden hebt geregistreerd, kunt u het geregistreerde model downloaden of implementeren en alle bestanden ontvangen die u hebt geregistreerd.

> [!TIP] 
> Het registreren van een model voor het bijhouden van versies wordt aanbevolen, maar niet vereist. Als u liever wilt doorgaan zonder een model te registreren, moet u een bronmap opgeven [in uw InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) of [inferenceconfig.jsaan](./reference-azure-machine-learning-cli.md#inference-configuration-schema) en ervoor zorgen dat uw model zich in die bronmap bevindt.

> [!TIP]
> Wanneer u een model registreert, geeft u het pad op van een cloudlocatie (van een trainingsrun) of een lokale map. Dit pad is alleen om de bestanden te zoeken die moeten worden geüpload als onderdeel van het registratieproces. Deze hoeft niet overeen te komen met het pad dat wordt gebruikt in het invoerscript. Zie Modelbestanden zoeken [in uw invoerscript voor meer informatie.](./how-to-deploy-advanced-entry-script.md#load-registered-models)

> [!IMPORTANT]
> Wanneer u de optie Filteren op gebruikt op de pagina Modellen van Azure Machine Learning Studio, moet u in plaats van klanten gebruiken `Tags` `TagName : TagValue` `TagName=TagValue` (zonder ruimte)

De volgende voorbeelden laten zien hoe u een model registreert.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Een model registreren vanuit een Azure ML-trainingsrun

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

De `--asset-path` parameter verwijst naar de cloudlocatie van het model. In dit voorbeeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt opnemen in de modelregistratie, stelt u in op het pad van `--asset-path` een map die de bestanden bevat.

### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Als u meerdere bestanden wilt opnemen in de modelregistratie, stelt u in op het pad van `-p` een map die de bestanden bevat.

Raadpleeg de referentiedocumentatie `az ml model register` voor meer informatie [over](/cli/azure/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Een model registreren vanuit een Azure ML-trainingsrun

  Wanneer u de SDK gebruikt om een model te trainen, kunt u een [Run-object](/python/api/azureml-core/azureml.core.run.run) of een [AutoMLRun-object](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) ontvangen, afhankelijk van hoe u het model hebt getraind. Elk object kan worden gebruikt om een model te registreren dat is gemaakt door een experiment.

  + Een model van een `azureml.core.Run` object registreren:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    De `model_path` parameter verwijst naar de cloudlocatie van het model. In dit voorbeeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt opnemen in de modelregistratie, stelt u in op het pad van `model_path` een map die de bestanden bevat. Zie de documentatie Run.register_model [meer](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) informatie.

  + Een model van een `azureml.train.automl.run.AutoMLRun` object registreren:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In dit voorbeeld worden de parameters en niet opgegeven, zodat de iteratie met de beste primaire `metric` `iteration` metrische gegevens wordt geregistreerd. De `model_id` waarde die door de uitvoering wordt geretourneerd, wordt gebruikt in plaats van een modelnaam.

    Zie de documentatie voor AutoMLRun.register_model [informatie.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

    Als u een geregistreerd model wilt implementeren vanuit een , raden we u aan dit te doen via de implementatieknop met `AutoMLRun` één klik in Azure Machine Learning [Studio.](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) 
### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

U kunt een model registreren door het lokale pad van het model op te geven. U kunt het pad van een map of één bestand verstrekken. U kunt deze methode gebruiken om modellen te registreren die zijn getraind met Azure Machine Learning en vervolgens gedownload. U kunt deze methode ook gebruiken om modellen te registreren die zijn getraind buiten Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **De SDK en ONNX gebruiken**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Als u meerdere bestanden wilt opnemen in de modelregistratie, stelt u in op het pad van `model_path` een map die de bestanden bevat.

Zie de documentatie voor de [modelklasse voor meer informatie.](/python/api/azureml-core/azureml.core.model.model)

Zie How to deploy an existing model (Een bestaand model implementeren) voor meer informatie over het werken met modellen die buiten Azure Machine Learning [zijn getraind.](how-to-deploy-existing-model.md)

---

## <a name="define-an-entry-script"></a>Een invoerscript definiëren

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Een deferentieconfiguratie definiëren


In een deferentieconfiguratie wordt beschreven hoe u de webservice met uw model in kunt stellen. Deze wordt later gebruikt wanneer u het model implementeert.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Een minimale deferentieconfiguratie kan worden geschreven als:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir",
    "environment": {
    "docker": {
        "arguments": [],
        "baseDockerfile": null,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
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
                "conda-forge",
                "pytorch"
            ],
            "dependencies": [
                "python=3.6.2",
                "torchvision"
                {
                    "pip": [
                        "azureml-defaults",
                        "azureml-telemetry",
                        "scikit-learn==0.22.1",
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
```

Hiermee geeft u op dat de machine learning-implementatie het bestand in de map gebruikt voor het verwerken van binnenkomende aanvragen en dat deze de Docker-installatiebestand gebruikt met de Python-pakketten die zijn opgegeven in de `score.py` `./working_dir` `project_environment` omgeving.

[Zie dit artikel](./reference-azure-machine-learning-cli.md#inference-configuration-schema) voor een uitgebreidere bespreking van de deferentieconfiguraties. 

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt het volgende gedemonstreerd:

1. een [gecureerde omgeving laden](resource-curated-environments.md) vanuit uw werkruimte
1. De omgeving klonen
1. Opgeven `scikit-learn` als een afhankelijkheid.
1. De omgeving gebruiken om een InferenceConfig te maken

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Zie Omgevingen maken en beheren voor training en implementatie voor meer [informatie over omgevingen.](how-to-use-environments.md)

Zie de documentatie over de [deferenceConfig-klasse](/python/api/azureml-core/azureml.core.model.inferenceconfig) voor meer informatie over de deferenceconfiguratie.

---

> [!TIP] 
> Zie How to deploy a model using a custom Docker image (Een model implementeren met behulp van een aangepaste Docker-installatiebestand) voor meer informatie over het gebruik van een [aangepaste Docker-installatiebestand](how-to-deploy-custom-docker-image.md)met een deferentieconfiguratie.

## <a name="choose-a-compute-target"></a>Een rekendoel kiezen

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Een implementatieconfiguratie definiëren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

De beschikbare opties voor een implementatieconfiguratie verschillen, afhankelijk van het rekendoel dat u kiest.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Zie deze naslaginformatie [voor meer informatie.](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)

# <a name="python"></a>[Python](#tab/python)

Voordat u uw model implementeert, moet u de implementatieconfiguratie definiëren. *De implementatieconfiguratie is specifiek voor het rekendoel dat de webservice gaat hosten.* Als u bijvoorbeeld een model lokaal implementeert, moet u de poort opgeven waar de service aanvragen accepteert. De implementatieconfiguratie maakt geen deel uit van uw invoerscript. Het wordt gebruikt voor het definiëren van de kenmerken van het rekendoel dat als host voor het model en het invoerscript wordt gebruikt.

Mogelijk moet u ook de rekenresource maken, als u bijvoorbeeld nog geen AKS-exemplaar (Azure Kubernetes Service) aan uw werkruimte hebt gekoppeld.

De volgende tabel bevat een voorbeeld van het maken van een implementatieconfiguratie voor elk rekendoel:

| Rekendoel | Voorbeeld van implementatieconfiguratie |
| ----- | ----- |
| Lokaal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

De klassen voor lokale, Azure Container Instances en AKS-webservices kunnen worden geïmporteerd uit `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Uw machine learning implementeren

U bent nu klaar om uw model te implementeren. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Een geregistreerd model gebruiken

Als u uw model hebt geregistreerd in Azure Machine Learning werkruimte, vervangt u 'mymodel:1' door de naam van uw model en het versienummer.

```azurecli-interactive
az ml model deploy -n tutorial -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Een lokaal model gebruiken

Als u uw model liever niet registreert, kunt u de parameter 'sourceDirectory' in uw inferenceconfig.jsdoorgeven aan om een lokale map op te geven van waaruit u uw model wilt gebruiken.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json --name my_deploy
```

# <a name="python"></a>[Python](#tab/python)

In het onderstaande voorbeeld wordt een lokale implementatie gedemonstreerd. De syntaxis is afhankelijk van het rekendoel dat u in de vorige stap hebt gekozen.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie de documentatie voor [LocalWebservice,](/python/api/azureml-core/azureml.core.webservice.local.localwebservice) [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)en Webservice voor [meer informatie.](/python/api/azureml-core/azureml.core.webservice.webservice)

---

### <a name="understanding-service-state"></a>Servicetoestand begrijpen

Tijdens de modelimplementatie ziet u mogelijk de status van de service veranderen terwijl deze volledig wordt geïmplementeerd.

In de volgende tabel worden de verschillende service-staten beschreven:

| Status van webservice | Description | Laatste status?
| ----- | ----- | ----- |
| Overgang | De service is bezig met de implementatie. | No |
| Niet in orde | De service is geïmplementeerd, maar is momenteel niet bereikbaar.  | No |
| Niet-bewerkbaar | De service kan op dit moment niet worden geïmplementeerd vanwege een gebrek aan resources. | No |
| Mislukt | De service kan niet worden geïmplementeerd vanwege een fout of crash. | Yes |
| In orde | De service is in orde en het eindpunt is beschikbaar. | Yes |

> [!TIP]
> Tijdens de implementatie worden Docker-afbeeldingen voor rekendoelen gebouwd en geladen vanuit Azure Container Registry (ACR). Standaard maakt Azure Machine Learning een ACR die gebruikmaakt van de *servicelaag* Basic. Het wijzigen van de ACR voor uw werkruimte in de Standard- of Premium-laag kan de tijd verminderen die nodig is om afbeeldingen te bouwen en te implementeren op uw rekendoelen. Zie servicelagen voor [Azure Container Registry meer informatie.](../container-registry/container-registry-skus.md)

> [!NOTE]
> Als u een model implementeert voor Azure Kubernetes Service (AKS), raden we u aan om Azure Monitor [voor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) dat cluster in te stellen. Dit helpt u inzicht te krijgen in de algehele clustertoestand en het resourcegebruik. Mogelijk vindt u de volgende resources ook nuttig:
>
> * [Controleren op Resource Health gebeurtenissen die van invloed zijn op uw AKS-cluster](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service Diagnostics](../aks/concepts-diagnostics.md)
>
> Als u een model probeert te implementeren in een cluster dat niet in orde of overbelast is, zal dit naar verwachting problemen ervaren. Neem contact op met de ondersteuning van AKS als u hulp nodig hebt bij het oplossen van problemen met AKS-clusters.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Batchdeferentie
Azure Machine Learning compute-doelen worden gemaakt en beheerd door Azure Machine Learning. Ze kunnen worden gebruikt voor batchvoorspelling van Azure Machine Learning pijplijnen.

Zie Batchvoorspellingen uitvoeren voor een overzicht van batchdeferentie Azure Machine Learning [Compute.](tutorial-pipeline-batch-scoring-classification.md)

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge de deferentie
Ondersteuning voor het implementeren naar de edge is in preview. Zie Deploy Azure Machine Learning as an IoT Edge module (Een IoT Edge [implementeren) voor meer informatie.](../iot-edge/tutorial-deploy-machine-learning.md)

## <a name="delete-resources"></a>Resources verwijderen

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Gebruik om een geïmplementeerde webservice te `az ml service <name of webservice>` verwijderen.

Als u een geregistreerd model uit uw werkruimte wilt verwijderen, gebruikt u `az ml model delete <model id>`

Lees meer over [het verwijderen van een webservice](/cli/azure/ml/service#az_ml_service_delete) en het verwijderen van een [model](/cli/azure/ml/model#az_ml_model_delete).

# <a name="python"></a>[Python](#tab/python)

Gebruik om een geïmplementeerde webservice te `service.delete()` verwijderen.
Als u een geregistreerd model wilt verwijderen, gebruikt `model.delete()` u .

Zie de documentatie voor [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) en [Model.delete() voor meer informatie.](/python/api/azureml-core/azureml.core.model.model#delete--)

---

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Clienttoepassingen maken om webservices te gebruiken](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [Implementatie met één klik voor geautomatiseerde ML-runs in de Azure Machine Learning-studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurteniswaarschuwingen en triggers maken voor modelimplementaties](how-to-use-event-grid.md)