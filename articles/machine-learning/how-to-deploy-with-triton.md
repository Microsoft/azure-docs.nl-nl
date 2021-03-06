---
title: Model met hoge prestaties voor het uitvoeren van Triton (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw model met de NVIDIA Triton in-Server voor in-beinterferentie in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 47d2c8865109e8ef43317b3c4a19c36e692aff91
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218839"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Hoge prestaties met een Triton-inrichtings server (preview-versie) 

Meer informatie over het gebruik van de [NVIDIA Triton-server](https://aka.ms/nvidia-triton-docs) voor inactiviteit voor het verbeteren van de prestaties van de webservice die wordt gebruikt voor model deinterferentie.

Een van de manieren om een model te implementeren, is als een webservice. Bijvoorbeeld een implementatie naar Azure Kubernetes service of Azure Container Instances. Azure Machine Learning maakt standaard gebruik van een single-threaded, *Algemeen* webframework voor web service-implementaties.

Triton is een raam werk dat is *geoptimaliseerd voor* demijnen. Het biedt een beter gebruik van Gpu's en een rendabelere interferentie. Aan de server zijde worden inkomende aanvragen in batches verzonden en worden deze batches ingediend voor een afleiding. Met batch verwerking kunt u beter GPU-bronnen gebruiken. Dit is een belang rijk onderdeel van de prestaties van Triton.

> [!IMPORTANT]
> Het gebruik van Triton voor implementatie vanuit Azure Machine Learning is momenteel beschikbaar als __Preview-versie__. De functionaliteit van de preview-versie wordt mogelijk niet gedekt door de klant ondersteuning. Voor meer informatie raadpleegt [u de aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> De code fragmenten in dit document zijn bedoeld als illustratief en er wordt mogelijk geen volledige oplossing weer gegeven. Zie de [end-to-end-voor beelden van Triton in azure machine learning](https://aka.ms/triton-aml-sample)voor werk voorbeeld code.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).
* Vertrouwd met [het implementeren van een model](how-to-deploy-and-where.md) met Azure machine learning.
* De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/?view=azure-ml-py) **of** de [Azure cli](/cli/azure/) -en [machine learning-extensie](reference-azure-machine-learning-cli.md).
* Een werkende installatie van docker voor lokale tests. Zie [Orientation en Setup](https://docs.docker.com/get-started/) in de docker-documentatie voor meer informatie over het installeren en valideren van docker.

## <a name="architectural-overview"></a>Architectuuroverzicht

Voordat u Triton voor uw eigen model probeert te gebruiken, is het belang rijk om te begrijpen hoe het werkt met Azure Machine Learning en hoe het wordt vergeleken met een standaard implementatie.

**Standaard implementatie zonder Triton**

* Meerdere [Gunicorn](https://gunicorn.org/) -werk rollen worden gestart om inkomende aanvragen gelijktijdig te verwerken.
* Deze werk nemers verwerken de pre-verwerking, het model aanroepen en naverwerking. 
* Clients gebruiken de __Score-URI van Azure ml__. Bijvoorbeeld `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normaal, niet-Triton, implementatie architectuur diagram":::

**Direct implementeren met Triton**

* Aanvragen gaan rechtstreeks naar de Triton-server.
* Triton verwerkt aanvragen in batches om het GPU-gebruik te maximaliseren.
* De client gebruikt de __Triton-URI__ om aanvragen te doen. Bijvoorbeeld `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Inferenceconfig-implementatie met alleen Triton en geen python-middleware":::

**Configuratie-implementatie met Triton**

* Meerdere [Gunicorn](https://gunicorn.org/) -werk rollen worden gestart om inkomende aanvragen gelijktijdig te verwerken.
* De aanvragen worden doorgestuurd naar de **Triton-server**. 
* Triton verwerkt aanvragen in batches om het GPU-gebruik te maximaliseren.
* De client gebruikt de __Score-URI van Azure ml__ om aanvragen uit te voeren. Bijvoorbeeld `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Implementatie met Triton en python-middleware":::

De werk stroom voor het gebruik van Triton voor uw model implementatie is:

1. Uw model rechtstreeks bij Triton bedienen.
1. Controleer of u aanvragen kunt verzenden naar uw Triton-geïmplementeerde model.
1. Beschrijving Een laag van python-middleware maken voor pre-en naverwerking aan de server zijde

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Triton implementeren zonder python vóór en na de verwerking

Voer eerst de volgende stappen uit om te controleren of de Triton in-Server voor uw model kan dienen.

### <a name="optional-define-a-model-config-file"></a>Beschrijving Een model configuratie bestand definiëren

Het configuratie bestand van het model vertelt Triton hoeveel invoer moet worden verwacht en van welke dimensies deze invoer zal zijn. Zie [model configuratie](https://aka.ms/nvidia-triton-docs) in de NVIDIA-documentatie voor meer informatie over het maken van het configuratie bestand.

> [!TIP]
> We gebruiken de `--strict-model-config=false` optie bij het starten van de Triton-inrichtings server, wat betekent dat u geen bestand hoeft op te geven `config.pbtxt` voor ONNX-of tensor flow-modellen.
> 
> Zie voor meer informatie over deze optie [gegenereerde model configuratie](https://aka.ms/nvidia-triton-docs) in de NVIDIA-documentatie.

### <a name="use-the-correct-directory-structure"></a>De juiste mapstructuur gebruiken

Bij het registreren van een model met Azure Machine Learning kunt u afzonderlijke bestanden of een mapstructuur registreren. Als u Triton wilt gebruiken, moet de model registratie voor een directory structuur zijn die een map bevat met de naam `triton` . De algemene structuur van deze map is:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Deze mapstructuur is een Triton-model opslagplaats en is vereist voor uw model (s) voor gebruik met Triton. Zie [Triton model-opslag](https://aka.ms/nvidia-triton-docs) plaatsen in de NVIDIA-documentatie voor meer informatie.

### <a name="register-your-triton-model"></a>Uw Triton-model registreren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

`az ml model register`Raadpleeg de [referentie documentatie](/cli/azure/ext/azure-cli-ml/ml/model)voor meer informatie.

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Zie de documentatie voor de [model klasse](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py)voor meer informatie.

---

### <a name="deploy-your-model"></a>Uw model implementeren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Als u een met GPU ingeschakeld Azure Kubernetes-service cluster met de naam ' AKS-GPU ' hebt gemaakt via Azure Machine Learning, kunt u de volgende opdracht gebruiken om uw model te implementeren.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

Raadpleeg [deze documentatie voor meer informatie over het implementeren van modellen](how-to-deploy-and-where.md).

### <a name="call-into-your-deployed-model"></a>Aanroepen naar uw geïmplementeerde model

U kunt eerst uw score-URI en Bearer-tokens ophalen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Controleer vervolgens of uw service wordt uitgevoerd door het volgende te doen: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Deze opdracht retourneert informatie die lijkt op het volgende. Let op de `200 OK` ; deze status betekent dat de webserver wordt uitgevoerd.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Zodra u een status controle hebt uitgevoerd, kunt u een client maken voor het verzenden van gegevens naar Triton. Zie de [client voorbeelden](https://aka.ms/nvidia-client-examples) in de NVIDIA-documentatie voor meer informatie over het maken van een client. Er zijn ook [python-voor beelden in de Triton-github](https://aka.ms/nvidia-triton-docs).

Als u op dit punt geen python vóór en naverwerking wilt toevoegen aan uw geïmplementeerde webservice, kunt u dit doen. Als u deze voorafgaande en verwerkings logica wilt toevoegen, leest u op.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>Beschrijving Opnieuw implementeren met een python-invoer script voor vóór en na de verwerking

Nadat u hebt gecontroleerd of de Triton uw model kan leveren, kunt u vooraf en post code toevoegen door een _invoer script_ te definiëren. Dit bestand heeft de naam `score.py` . Zie [een vermeldings script definiëren](how-to-deploy-and-where.md#define-an-entry-script)voor meer informatie over invoer scripts.

De twee belangrijkste stappen zijn het initialiseren van een Triton HTTP-client in uw `init()` methode en voor het aanroepen van die client in uw `run()` functie.

### <a name="initialize-the-triton-client"></a>De Triton-client initialiseren

Neem code zoals het volgende voor beeld op in het `score.py` bestand. Triton in Azure Machine Learning verwacht te worden behandeld op localhost, poort 8000. In dit geval bevindt localhost zich in de docker-installatie kopie voor deze implementatie, en niet voor een poort op de lokale computer:

> [!TIP]
> Het `tritonhttpclient` PIP-pakket is opgenomen in de gecuratore `AzureML-Triton` omgeving. u hoeft deze niet op te geven als een PIP-afhankelijkheid.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Uw score script aanpassen om Triton aan te roepen

In het volgende voor beeld ziet u hoe u de meta gegevens dynamisch kunt aanvragen voor het model:

> [!TIP]
> U kunt de meta gegevens van modellen die zijn geladen met Triton dynamisch aanvragen met behulp `.get_model_metadata` van de methode van de Triton-client. Bekijk het voor beeld van het [notitie blok](https://aka.ms/triton-aml-sample) voor een gebruiks voorbeeld.

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Opnieuw implementeren met een configuratie voor afnemen van een interferentie

Met een Afleidings configuratie kunt u een invoer script en het Azure Machine Learning-implementatie proces gebruiken met behulp van de python-SDK of Azure CLI.

> [!IMPORTANT]
> U moet de met de curator gevoerde `AzureML-Triton` [omgeving](./resource-curated-environments.md)opgeven.
>
> Het code voorbeeld van python wordt gekloond `AzureML-Triton` in een andere omgeving met de naam `My-Triton` . De Azure CLI-code gebruikt ook deze omgeving. Zie de referentie [environment. Clone ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) voor meer informatie over het klonen van een omgeving.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

> [!TIP]
> Zie voor meer informatie over het maken van een Afleidings configuratie het [schema](./reference-azure-machine-learning-cli.md#inference-configuration-schema)voor het afstellen van interferentie.

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Nadat de implementatie is voltooid, wordt de Score-URI weer gegeven. Voor deze lokale implementatie is het `http://localhost:6789/score` . Als u in de Cloud implementeert, kunt u de opdracht [AZ ml service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI gebruiken om de Score-URI op te halen.

Zie [een model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)voor meer informatie over het maken van een client die inactiviteits aanvragen verzendt naar de Score-URI.

### <a name="setting-the-number-of-workers"></a>Het aantal werk rollen instellen

Stel de omgevings variabele in om het aantal werk nemers in uw implementatie in te stellen `WORKER_COUNT` . Als u een [omgevings](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) object hebt aangeroepen `env` , kunt u het volgende doen:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Hiermee wordt het aantal werk nemers dat u opgeeft, door Azure ML genoteerd.


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent de Azure Machine Learning-werk ruimte te blijven gebruiken, maar de geïmplementeerde service wilt verwijderen, gebruikt u een van de volgende opties:


# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>Volgende stappen

* [Bekijk end-to-end-voor beelden van Triton in Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Bekijk de [voor beelden van Triton-clients](https://aka.ms/nvidia-client-examples)
* Lees de [Triton-Server documentatie](https://aka.ms/nvidia-triton-docs) voor deservering
* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)