---
title: Krachtige modelprestaties met Triton (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw model met NVIDIA Triton Inference Server in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 8775696a35bfccc363aa2c6ec06c6c44115916b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479267"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Krachtige prestaties met Triton Inference Server (preview) 

Meer informatie over het gebruik [van NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) om de prestaties te verbeteren van de webservice die wordt gebruikt voor modeldeferentie.

Een van de manieren om een model voor de deferentie te implementeren, is als een webservice. Bijvoorbeeld een implementatie naar Azure Kubernetes Service of Azure Container Instances. Standaard maakt Azure Machine Learning gebruik van een web-framework met één thread *voor* algemeen gebruik voor webservice-implementaties.

Triton is een framework dat is *geoptimaliseerd voor de deferie*. Het biedt een beter gebruik van GPU's en een rendabele deferentie. Aan de serverzijde worden binnenkomende aanvragen in batches uitgevoerd en worden deze batches voor de deferie ingediend. Batching maakt beter gebruik van GPU-resources en is een belangrijk onderdeel van de prestaties van Triton.

> [!IMPORTANT]
> Triton gebruiken voor implementatie vanuit Azure Machine Learning is momenteel in __preview.__ De preview-functionaliteit wordt mogelijk niet gedekt door de klantondersteuning. Zie voor meer informatie de [aanvullende gebruiksvoorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> De codefragmenten in dit document zijn bedoeld ter illustratie en bieden mogelijk geen volledige oplossing. Zie voor werkende voorbeeldcode [de end-to-end-voorbeelden van Triton in Azure Machine Learning](https://aka.ms/triton-aml-sample).

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) is een opensource-software van derden die is geïntegreerd in Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, kunt u de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)
* Bekendheid met [het implementeren van een model](how-to-deploy-and-where.md) met Azure Machine Learning.
* De [Azure Machine Learning-SDK voor Python](/python/api/overview/azure/ml/) **of** de [Azure CLI](/cli/azure/) en [machine learning-extensie](reference-azure-machine-learning-cli.md).
* Een werkende installatie van Docker voor lokaal testen. Zie Stand en installatie [in](https://docs.docker.com/get-started/) de docker-documentatie voor meer informatie over het installeren en valideren van Docker.

## <a name="architectural-overview"></a>Architectuuroverzicht

Voordat u Triton gaat gebruiken voor uw eigen model, is het belangrijk om te begrijpen hoe het werkt met Azure Machine Learning en hoe het zich verhoudt tot een standaardimplementatie.

**Standaardimplementatie zonder Triton**

* Meerdere [Gunicorn-medewerkers](https://gunicorn.org/) worden gestart om gelijktijdig binnenkomende aanvragen te verwerken.
* Deze werksters verwerken voorverwerking, het aanroepen van het model en naverwerking. 
* Clients gebruiken de __Azure ML-scoring-URI__. Bijvoorbeeld `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagram van normale, niet-tritone implementatiearchitectuur":::

**Rechtstreeks implementeren met Triton**

* Aanvragen gaan rechtstreeks naar de Triton-server.
* Triton verwerkt aanvragen in batches om het GPU-gebruik te maximaliseren.
* De client gebruikt de __Triton-URI om__ aanvragen te maken. Bijvoorbeeld `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Deferenceconfig-implementatie met alleen Triton en geen Python-middleware":::

**Implementatie van de deferentieconfiguratie met Triton**

* Meerdere [Gunicorn-medewerkers](https://gunicorn.org/) worden gestart om gelijktijdig binnenkomende aanvragen te verwerken.
* De aanvragen worden doorgestuurd naar de **Triton-server**. 
* Triton verwerkt aanvragen in batches om het GPU-gebruik te maximaliseren.
* De client gebruikt de __Scoring-URI van Azure ML om__ aanvragen te maken. Bijvoorbeeld `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Implementatie met Triton- en Python-middleware":::

De werkstroom voor het gebruik van Triton voor uw modelimplementatie is:

1. Uw model rechtstreeks bedienen met Triton.
1. Controleer of u aanvragen naar uw door Triton geïmplementeerde model kunt verzenden.
1. (Optioneel) Een laag Python-middleware maken voor voor- en naverwerking aan serverzijde

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Triton implementeren zonder voor- en naverwerking van Python

Volg eerst de onderstaande stappen om te controleren of de Triton Inference Server uw model kan bedienen.

### <a name="optional-define-a-model-config-file"></a>(Optioneel) Een model config-bestand definiëren

Het modelconfiguratiebestand vertelt Triton hoeveel invoer u kunt verwachten en welke dimensies deze invoer zal zijn. Zie Modelconfiguratie [in](https://aka.ms/nvidia-triton-docs) de NVIDIA-documentatie voor meer informatie over het maken van het configuratiebestand.

> [!TIP]
> We gebruiken de optie bij het starten van de Triton Inference Server, wat betekent dat u geen bestand hoeft op te geven voor `--strict-model-config=false` `config.pbtxt` ONNX- of TensorFlow-modellen.
> 
> Zie Gegenereerde [modelconfiguratie](https://aka.ms/nvidia-triton-docs) in de NVIDIA-documentatie voor meer informatie over deze optie.

### <a name="use-the-correct-directory-structure"></a>De juiste mapstructuur gebruiken

Wanneer u een model registreert bij Azure Machine Learning, kunt u afzonderlijke bestanden of een mapstructuur registreren. Als u Triton wilt gebruiken, moet de modelregistratie zijn voor een directorystructuur die een map met de naam `triton` bevat. De algemene structuur van deze map is:

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
> Deze mapstructuur is een Triton Model Repository en is vereist voor uw model(s) om met Triton te kunnen werken. Zie [Triton Model Repositories (Opslagplaatsen voor tritonmodellen)](https://aka.ms/nvidia-triton-docs) in de NVIDIA-documentatie voor meer informatie.

### <a name="register-your-triton-model"></a>Uw Triton-model registreren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Raadpleeg de referentiedocumentatie `az ml model register` voor meer informatie [over](/cli/azure/ext/azure-cli-ml/ml/model).

Bij het registreren van het model in Azure Machine Learning, moet de waarde voor de parameter de naam zijn van de bovenliggende `--model-path  -p` map van de Triton.  
In het bovenstaande voorbeeld  `--model-path` is 'modellen'.

De waarde voor `--name  -n` parameter, â€ ̃ my_triton_modelâ€™ in het voorbeeld, is de modelnaam die bekend is bij Azure Machine Learning-werkruimte. 

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
Zie de documentatie voor de [modelklasse voor meer informatie.](/python/api/azureml-core/azureml.core.model.model)

---

### <a name="deploy-your-model"></a>Uw model implementeren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Als u een cluster met GPU Azure Kubernetes Service met de naam 'aks-gpu' hebt gemaakt via Azure Machine Learning, kunt u de volgende opdracht gebruiken om uw model te implementeren.

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

Zie [deze documentatie voor meer informatie over het implementeren van modellen.](how-to-deploy-and-where.md)

### <a name="call-into-your-deployed-model"></a>Het geïmplementeerde model aanroepen

Haal eerst uw score-URI en Bearer-tokens op.

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

Deze opdracht retourneert informatie die vergelijkbaar is met de volgende. Let op `200 OK` de . Deze status betekent dat de webserver wordt uitgevoerd.

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

Zodra u een statuscontrole hebt uitgevoerd, kunt u een client maken om gegevens naar Triton te verzenden voor de deferie. Zie de clientvoorbeelden [in](https://aka.ms/nvidia-client-examples) de NVIDIA-documentatie voor meer informatie over het maken van een client. Er zijn ook [Python-voorbeelden op de Triton GitHub](https://aka.ms/nvidia-triton-docs).

Als u op dit moment geen Python-voor- en naverwerking wilt toevoegen aan uw geïmplementeerde webservice, bent u mogelijk klaar. Als u deze voor- en naverwerkingslogica wilt toevoegen, lees dan verder.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(Optioneel) Opnieuw implementeren met een Python-invoerscript voor voor- en naverwerking

Nadat u hebt gecontroleerd of de Triton uw model kan bedienen, kunt u pre- en post-processing-code toevoegen door een invoerscript _te definiëren._ Dit bestand heet `score.py` . Zie Een invoerscript definiëren voor meer informatie over [invoerscripts.](how-to-deploy-and-where.md#define-an-entry-script)

De twee belangrijkste stappen zijn het initialiseren van een Triton HTTP-client in uw methode en het aanroepen van die `init()` client in uw `run()` functie.

### <a name="initialize-the-triton-client"></a>De Triton-client initialiseren

Neem code zoals in het volgende voorbeeld op in uw `score.py` bestand. Triton in Azure Machine Learning wordt verwacht te worden aangepakt op localhost, poort 8000. In dit geval maakt localhost voor deze implementatie gebruik van de Docker-installatier, niet van een poort op uw lokale computer:

> [!TIP]
> Het pip-pakket is opgenomen in de gecureerde omgeving, dus u hoeft het niet op te geven `tritonhttpclient` `AzureML-Triton` als een PIP-afhankelijkheid.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Uw scorescript wijzigen om Triton aan te roepen

In het volgende voorbeeld wordt gedemonstreerd hoe u de metagegevens voor het model dynamisch aanvraagt:

> [!TIP]
> U kunt dynamisch de metagegevens aanvragen van modellen die zijn geladen met Triton met behulp van de `.get_model_metadata` methode van de Triton-client. Zie het [voorbeeldnote notebook](https://aka.ms/triton-aml-sample) voor een voorbeeld van het gebruik ervan.

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

### <a name="redeploy-with-an-inference-configuration"></a>Opnieuw deployeren met een deference-configuratie

Met een deferentieconfiguratie kunt u een invoerscript gebruiken, evenals het Azure Machine Learning implementatieproces met behulp van de Python SDK of Azure CLI.

> [!IMPORTANT]
> U moet de `AzureML-Triton` [gecureerde omgeving opgeven.](./resource-curated-environments.md)
>
> Het Python-codevoorbeeld wordt gekloond `AzureML-Triton` naar een andere omgeving met de naam `My-Triton` . De Azure CLI-code maakt ook gebruik van deze omgeving. Zie de naslaginformatie over [Environment.Clone()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) voor meer informatie over het klonen van een omgeving.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

> [!TIP]
> Zie het deferentieconfiguratieschema voor meer informatie over het maken van een [deferentieconfiguratie.](./reference-azure-machine-learning-cli.md#inference-configuration-schema)

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

Nadat de implementatie is voltooid, wordt de score-URI weergegeven. Voor deze lokale implementatie is dit `http://localhost:6789/score` . Als u implementeert in de cloud, kunt u de CLI-opdracht [az ml service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) gebruiken om de scoring-URI op te halen.

Zie Consume a model deployed as a web service (Een model gebruiken dat is geïmplementeerd als [een webservice)](how-to-consume-web-service.md)voor meer informatie over het maken van een client die de deferentieaanvragen naar de scoring-URI verzendt.

### <a name="setting-the-number-of-workers"></a>Het aantal werknemers instellen

Als u het aantal werkrol in uw implementatie wilt instellen, stelt u de omgevingsvariabele `WORKER_COUNT` in. Gezien u een [omgevingsobject hebt](/python/api/azureml-core/azureml.core.environment.environment) met de naam , kunt u het `env` volgende doen:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Dit geeft Azure ML de tijd om het aantal werksters op te geven dat u opgeeft.


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om de werkruimte Azure Machine Learning blijven gebruiken, maar de geïmplementeerde service wilt verwijderen, gebruikt u een van de volgende opties:


# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>Problemen oplossen

* [Problemen met een mislukte implementatie oplossen,](how-to-troubleshoot-deployment.md)informatie over het oplossen en oplossen van veelvoorkomende fouten die kunnen optreden bij het implementeren van een model.

* Als implementatielogboeken laten zien **dat TritonServer** niet kan worden starten, raadpleegt u de documentatie [van Nvidiaâ€™s open source starten.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Volgende stappen

* [Bekijk end-to-end-voorbeelden van Triton in Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Voorbeelden van [Triton-client bekijken](https://aka.ms/nvidia-client-examples)
* De documentatie [voor Triton Inference Server lezen](https://aka.ms/nvidia-triton-docs)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
