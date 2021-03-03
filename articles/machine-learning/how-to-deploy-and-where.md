---
title: machine learning modellen implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe en waar machine learning modellen moeten worden geïmplementeerd. Implementeren in Azure Container Instances, Azure Kubernetes service, Azure IoT Edge en FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 01/13/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
adobe-target: true
ms.openlocfilehash: f8865c9e6726a19e5e215886f92507734ebf0662
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657312"
---
# <a name="deploy-machine-learning-models-to-azure"></a>machine learning modellen implementeren in azure

Meer informatie over het implementeren van uw machine learning of een diep leer model als een webservice in de Azure-Cloud. U kunt ook implementeren op Azure IoT Edge apparaten.

De werkstroom is vrijwel altijd hetzelfde, ongeacht waar u het model implementeert:

1. Registreer het model (optioneel, zie hieronder).
1. Een configuratie voor een afleiding voorbereiden (tenzij u een [implementatie zonder code](./how-to-deploy-no-code-deployment.md)gebruikt).
1. Een script voor een vermelding voorbereiden (tenzij u [geen code-implementatie](./how-to-deploy-no-code-deployment.md)gebruikt).
1. Kies een compute-doel.
1. Implementeer het model op het rekendoel.
1. Test de resulterende webservice.

Zie [modellen beheren, implementeren en bewaken met Azure machine learning](concept-model-management-and-deployment.md)voor meer informatie over de concepten die zijn betrokken bij de machine learning implementatie werk stroom.

## <a name="prerequisites"></a>Vereisten

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.
- Een model. Als u geen getraind model hebt, kunt u het model en de afhankelijkheids bestanden van [deze zelf studie](https://aka.ms/azml-deploy-cloud)gebruiken.
- De [Azure-extensie (opdracht regel Interface) voor de machine learning-service](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.
- Een model. Als u geen getraind model hebt, kunt u het model en de afhankelijkheids bestanden van [deze zelf studie](https://aka.ms/azml-deploy-cloud)gebruiken.
- De [Azure machine learning-Software Development Kit (SDK) voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

---

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werkruimte

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Volg de instructies in de Azure CLI-documentatie voor [het instellen van uw abonnements context](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Ga vervolgens als volgt te werk:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

Als u de werk ruimten wilt zien waartoe u toegang hebt.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Zie de documentatie van [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true#workspace) voor meer informatie over het gebruik van de SDK om verbinding te maken met een werk ruimte.


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Uw model registreren (optioneel)

Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in de werk ruimte. Nadat u de bestanden hebt geregistreerd, kunt u het geregistreerde model downloaden of implementeren en alle bestanden ontvangen die u hebt geregistreerd.

> [!TIP] 
> Het registreren van een model voor versie tracering wordt aanbevolen, maar is niet vereist. Als u liever door wilt gaan zonder een model te registreren, moet u een bronmap opgeven in uw [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) of [inferenceconfig.jsop](./reference-azure-machine-learning-cli.md#inference-configuration-schema) en ervoor zorgen dat uw model zich in die bron directory bevindt.

> [!TIP]
> Wanneer u een model registreert, geeft u het pad op naar een Cloud locatie (van een trainings uitvoering) of van een lokale map. Dit pad is alleen bedoeld voor het vinden van de bestanden die worden geüpload als onderdeel van het registratie proces. Het hoeft niet overeen te komen met het pad dat wordt gebruikt in het vermeldings script. Zie voor meer informatie [model bestanden zoeken in uw invoer script](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> Bij gebruik van `Tags` de optie filteren op de pagina modellen van Azure machine learning Studio in plaats van gebruik te maken van `TagName : TagValue` klanten `TagName=TagValue` (zonder ruimte)

De volgende voor beelden laten zien hoe u een model kunt registreren.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Een model registreren vanuit een Azure ML-trainings uitvoering

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

De `--asset-path` para meter verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `--asset-path` u het pad in naar een map die de bestanden bevat.

### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `-p` u het pad in naar een map die de bestanden bevat.

`az ml model register`Raadpleeg de [referentie documentatie](/cli/azure/ext/azure-cli-ml/ml/model)voor meer informatie.

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Een model registreren vanuit een Azure ML-trainings uitvoering

  Wanneer u de SDK gebruikt voor het trainen van een model, kunt u een [uitvoerings](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py) object of een [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) -object ontvangen, afhankelijk van hoe u het model hebt getraind. Elk object kan worden gebruikt voor het registreren van een model dat is gemaakt door een experiment.

  + Een model van een `azureml.core.Run` object registreren:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    De `model_path` para meter verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `model_path` u het pad in naar een map die de bestanden bevat. Zie de [Run.register_model](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) -documentatie voor meer informatie.

  + Een model van een `azureml.train.automl.run.AutoMLRun` object registreren:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In dit voor beeld `metric` worden de `iteration` para meters en niet opgegeven, zodat de iteratie met de beste primaire metriek wordt geregistreerd. De `model_id` waarde die wordt geretourneerd door de uitvoering, wordt gebruikt in plaats van een model naam.

    Zie de [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) -documentatie voor meer informatie.

### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

U kunt een model registreren door het lokale pad van het model op te geven. U kunt het pad naar een map of één bestand opgeven. U kunt deze methode gebruiken om modellen te registreren die zijn getraind met Azure Machine Learning en vervolgens te downloaden. U kunt deze methode ook gebruiken voor het registreren van modellen die buiten Azure Machine Learning worden getraind.

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

  Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `model_path` u het pad in naar een map die de bestanden bevat.

Zie de documentatie voor de [model klasse](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py)voor meer informatie.

Zie [een bestaand model implementeren](how-to-deploy-existing-model.md)voor meer informatie over het werken met modellen die buiten Azure machine learning zijn getraind.

---

## <a name="define-an-entry-script"></a>Een invoer script definiëren

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Een configuratie voor het afstellen van een interferentie definiëren


Een configuratie voor het afwijzen van een interferentie beschrijft het instellen van de webservice die uw model bevat. Het wordt later gebruikt wanneer u het model implementeert.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Er kan een minimale configuratie van de inmodus worden geschreven als:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

Hiermee geeft u op dat de machine learning-implementatie het bestand `score.py` in de `./working_dir` Directory gebruikt voor het verwerken van binnenkomende aanvragen.

[Raadpleeg dit artikel](./reference-azure-machine-learning-cli.md#inference-configuration-schema) voor een uitgebreidere bespreking van de configuraties voor het afzien van de configuratie. 

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u:

1. een [gecurede omgeving](resource-curated-environments.md) laden vanuit uw werk ruimte
1. De omgeving klonen
1. `scikit-learn`Als een afhankelijkheid opgeven.
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

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over omgevingen.

Zie de documentatie van [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) voor meer informatie over de configuratie van de afleiding.

---

> [!TIP] 
> Zie [een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste docker-installatie kopie met een afnemende configuratie.

## <a name="choose-a-compute-target"></a>Een reken doel kiezen

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Een implementatie configuratie definiëren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

De beschik bare opties voor een implementatie configuratie zijn afhankelijk van het reken doel dat u kiest.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Zie voor meer informatie [deze verwijzing](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

Voordat u uw model implementeert, moet u de implementatie configuratie definiëren. *De implementatie configuratie is specifiek voor het reken doel dat als host fungeert voor de webservice.* Wanneer u bijvoorbeeld een model lokaal implementeert, moet u de poort opgeven waar de service aanvragen accepteert. De implementatie configuratie maakt geen deel uit van het invoer script. Het wordt gebruikt voor het definiëren van de kenmerken van het Compute-doel die als host dienen voor het model en het script.

Mogelijk moet u ook de reken resource maken, als u bijvoorbeeld nog geen AKS-instantie (Azure Kubernetes service) hebt die aan uw werk ruimte is gekoppeld.

De volgende tabel bevat een voor beeld van het maken van een implementatie configuratie voor elk reken doel:

| Rekendoel | Implementatie configuratie-voor beeld |
| ----- | ----- |
| Lokaal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

De klassen voor lokale, Azure Container Instances-en AKS-webservices kunnen worden geïmporteerd uit `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Uw machine learning model implementeren

U bent nu klaar om uw model te implementeren. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Een geregistreerd model gebruiken

Als u het model in uw Azure Machine Learning-werk ruimte hebt geregistreerd, vervangt u ' mymodel: 1 ' door de naam van uw model en het versie nummer.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Een lokaal model gebruiken

Als u uw model liever niet wilt registreren, kunt u de para meter ' Source Directory ' in uw inferenceconfig.jsdoor geven aan om een lokale map op te geven van waaruit uw model moet worden gebruikt.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

In het onderstaande voor beeld wordt een lokale implementatie gedemonstreerd. De syntaxis is afhankelijk van het berekenings doel dat u in de vorige stap hebt gekozen.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie de documentatie voor [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py), [model. Deploy ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)en [webservice](/python/api/azureml-core/azureml.core.webservice.webservice?preserve-view=true&view=azure-ml-py)voor meer informatie.

---

### <a name="understanding-service-state"></a>Informatie over de service status

Tijdens de implementatie van het model ziet u mogelijk de wijziging van de service status terwijl deze volledig wordt geïmplementeerd.

In de volgende tabel worden de verschillende service statussen beschreven:

| Status van webservice | Beschrijving | Eind status?
| ----- | ----- | ----- |
| Overstappen | De service is in het implementatie proces. | Nee |
| Niet in orde | De service is geïmplementeerd, maar is momenteel niet bereikbaar.  | Nee |
| Unschedulable | De service kan op dit moment niet worden geïmplementeerd vanwege een gebrek aan resources. | Nee |
| Mislukt | De implementatie van de service is mislukt vanwege een fout of een crash. | Ja |
| In orde | De service is in orde en het eind punt is beschikbaar. | Ja |

> [!TIP]
> Tijdens de implementatie worden docker-installatie kopieën voor Compute-doelen gemaakt en geladen van Azure Container Registry (ACR). Azure Machine Learning maakt standaard een ACR die gebruikmaakt van de *Basic* -servicelaag. Het wijzigen van de ACR voor uw werk ruimte in de standaard-of Premium-laag kan de tijd verminderen die nodig is om installatie kopieën te bouwen en implementeren in uw reken doelen. Zie [Azure container Registry service lagen](../container-registry/container-registry-skus.md)voor meer informatie.

> [!NOTE]
> Als u een model implementeert in azure Kubernetes service (AKS), raden wij u aan [Azure monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) voor dat cluster in te scha kelen. Dit helpt u bij het begrijpen van het totale cluster status-en resource gebruik. U kunt ook de volgende bronnen nuttig vinden:
>
> * [Controleren op Resource Health gebeurtenissen die van invloed zijn op uw AKS-cluster](../aks/aks-resource-health.md)
> * [Diagnostische gegevens voor Azure Kubernetes-service](../aks/concepts-diagnostics.md)
>
> Als u probeert een model te implementeren in een beschadigde of overbelast cluster, worden er waarschijnlijk problemen ondervinden. Neem contact op met de ondersteuning van AKS als u hulp nodig hebt bij het oplossen van problemen met AKS-clusters.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Batch-deinterferentie
Azure Machine Learning Compute-doelen worden gemaakt en beheerd door Azure Machine Learning. Ze kunnen worden gebruikt voor batch voorspelling van Azure Machine Learning pijp lijnen.

Zie [batch voorspellingen uitvoeren](tutorial-pipeline-batch-scoring-classification.md)voor een overzicht van batch deinterferentie met Azure machine learning compute.

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge afleiding
Ondersteuning voor het implementeren naar de rand is in preview. Zie [Deploy Azure machine learning als een IOT Edge-module](../iot-edge/tutorial-deploy-machine-learning.md)voor meer informatie.

## <a name="delete-resources"></a>Resources verwijderen

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Als u een geïmplementeerde webservice wilt verwijderen, gebruikt u `az ml service <name of webservice>` .

Als u een geregistreerd model uit uw werk ruimte wilt verwijderen, gebruikt u `az ml model delete <model id>`

Meer informatie over [het verwijderen van een webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) en [het verwijderen van een model](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Als u een geïmplementeerde webservice wilt verwijderen, gebruikt u `service.delete()` .
Als u een geregistreerd model wilt verwijderen, gebruikt u `model.delete()` .

Zie de documentatie voor [webservice. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--) en [model. Delete ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)voor meer informatie.

---

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurtenis waarschuwingen en triggers maken voor model implementaties](how-to-use-event-grid.md)