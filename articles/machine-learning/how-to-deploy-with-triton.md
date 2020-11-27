---
title: Model met hoge prestaties voor het uitvoeren van Triton (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw model met de NVIDIA Triton in-Server voor in-beinterferentie in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 8e479367a04e105ae2111ce66707999aff7ef960
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302460"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Hoge prestaties met een Triton-inrichtings server (preview-versie) 

Meer informatie over het gebruik van de [NVIDIA Triton-server](https://developer.nvidia.com/nvidia-triton-inference-server) voor inactiviteit voor het verbeteren van de prestaties van de webservice die wordt gebruikt voor model deinterferentie.

Een van de manieren om een model te implementeren, is als een webservice. Bijvoorbeeld een implementatie naar Azure Kubernetes service of Azure Container Instances. Azure Machine Learning maakt standaard gebruik van een single-threaded, *Algemeen* webframework voor web service-implementaties.

Triton is een raam werk dat is *geoptimaliseerd voor* demijnen. Het biedt een beter gebruik van Gpu's en een rendabelere interferentie. Aan de server zijde worden inkomende aanvragen in batches verzonden en worden deze batches ingediend voor een afleiding. Met batch verwerking kunt u beter GPU-bronnen gebruiken. Dit is een belang rijk onderdeel van de prestaties van Triton.

> [!IMPORTANT]
> Het gebruik van Triton voor implementatie vanuit Azure Machine Learning is momenteel beschikbaar als __Preview-versie__. De functionaliteit van de preview-versie wordt mogelijk niet gedekt door de klant ondersteuning. Voor meer informatie raadpleegt [u de aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> De code fragmenten in dit document zijn bedoeld als illustratief en er wordt mogelijk geen volledige oplossing weer gegeven. Zie de [end-to-end-voor beelden van Triton in azure machine learning](https://github.com/Azure/azureml-examples/tree/main/tutorials)voor werk voorbeeld code.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).
* Vertrouwd met [het implementeren van een model](how-to-deploy-and-where.md) met Azure machine learning.
* De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/?view=azure-ml-py) **of** de [Azure cli](/cli/azure/?view=azure-cli-latest) -en [machine learning-extensie](reference-azure-machine-learning-cli.md).
* Een werkende installatie van docker voor lokale tests. Zie [Orientation en Setup](https://docs.docker.com/get-started/) in de docker-documentatie voor meer informatie over het installeren en valideren van docker.

## <a name="architectural-overview"></a>Architectuuroverzicht

Voordat u Triton voor uw eigen model probeert te gebruiken, is het belang rijk om te begrijpen hoe het werkt met Azure Machine Learning en hoe het wordt vergeleken met een standaard implementatie.

**Standaard implementatie zonder Triton**

* Meerdere [Gunicorn](https://gunicorn.org/) -werk rollen worden gestart om inkomende aanvragen gelijktijdig te verwerken.
* Deze werk nemers verwerken de pre-verwerking, het model aanroepen en naverwerking. 
* Voor aanvragen voor deaanvraaging wordt de __Score-URI__ gebruikt. Bijvoorbeeld `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normaal, niet-Triton, implementatie architectuur diagram":::

### <a name="setting-the-number-of-workers"></a>Het aantal werk rollen instellen

Stel de omgevings variabele in om het aantal werk nemers in uw implementatie in te stellen `WORKER_COUNT` . Als u een [omgevings](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) object hebt aangeroepen `env` , kunt u het volgende doen:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Hiermee wordt het aantal werk nemers dat u opgeeft, door Azure ML genoteerd.


**Configuratie-implementatie met Triton**

* Meerdere [Gunicorn](https://gunicorn.org/) -werk rollen worden gestart om inkomende aanvragen gelijktijdig te verwerken.
* De aanvragen worden doorgestuurd naar de **Triton-server**. 
* Triton verwerkt aanvragen in batches om het GPU-gebruik te maximaliseren.
* De client gebruikt de __Score__ ring-URI voor het maken van aanvragen. Bijvoorbeeld `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Inferenceconfig-implementatie met Triton":::

De werk stroom voor het gebruik van Triton voor uw model implementatie is:

1. Controleer of Triton kan dienen voor uw model.
1. Controleer of u aanvragen kunt verzenden naar uw Triton-geïmplementeerde model.
1. Neem uw Triton code op in uw AML-implementatie.

## <a name="verify-that-triton-can-serve-your-model"></a>Controleren of Triton uw model kan leveren

Voer eerst de volgende stappen uit om te controleren of de Triton in-Server voor uw model kan dienen.

### <a name="optional-define-a-model-config-file"></a>Beschrijving Een model configuratie bestand definiëren

Het configuratie bestand van het model vertelt Triton hoeveel invoer moet worden verwacht en van welke dimensies deze invoer zal zijn. Zie [model configuratie](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) in de NVIDIA-documentatie voor meer informatie over het maken van het configuratie bestand.

> [!TIP]
> We gebruiken de `--strict-model-config=false` optie bij het starten van de Triton-inrichtings server, wat betekent dat u geen bestand hoeft op te geven `config.pbtxt` voor ONNX-of tensor flow-modellen.
> 
> Zie voor meer informatie over deze optie [gegenereerde model configuratie](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) in de NVIDIA-documentatie.

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
> Deze mapstructuur is een Triton-model opslagplaats en is vereist voor uw model (s) voor gebruik met Triton. Zie [Triton model-opslag](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) plaatsen in de NVIDIA-documentatie voor meer informatie.

### <a name="test-with-triton-and-docker"></a>Testen met Triton en docker

Als u uw model wilt testen om er zeker van te zijn dat het wordt uitgevoerd met Triton, kunt u docker gebruiken. Met de volgende opdrachten wordt de Triton-container aan uw lokale computer door getrokken en wordt de Triton-server gestart:

1. Als u de installatie kopie voor de Triton-server op uw lokale computer wilt ophalen, gebruikt u de volgende opdracht:

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. Gebruik de volgende opdracht om de Triton-server te starten. Vervang door `<path-to-models/triton>` het pad naar de Triton-model opslagplaats die uw modellen bevat:

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Als u Windows gebruikt, wordt u mogelijk gevraagd netwerk verbindingen voor dit proces toe te staan wanneer u de opdracht voor het eerst uitvoert. Als dit het geval is, selecteert u om toegang in te scha kelen.

    Zodra deze is gestart, wordt de informatie die lijkt op de volgende tekst geregistreerd op de opdracht regel:

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    De eerste regel geeft het adres van de webservice aan. In dit geval `0.0.0.0:8000` is dat hetzelfde als `localhost:8000` .

1. Gebruik een hulp programma zoals krul om toegang te krijgen tot het eind punt van de status.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    Deze opdracht retourneert informatie die lijkt op het volgende. Let op de `200 OK` ; deze status betekent dat de webserver wordt uitgevoerd.

    ```bash
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

Na een eenvoudige status controle kunt u een client maken voor het verzenden van gegevens naar Triton. Zie de [client voorbeelden](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) in de NVIDIA-documentatie voor meer informatie over het maken van een client. Er zijn ook [python-voor beelden in de Triton-github](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples).

Zie voor meer informatie over het uitvoeren van Triton met behulp van docker [Triton uitvoeren op een systeem met een GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) en [Triton uitvoeren op een systeem zonder GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu).

### <a name="register-your-model"></a>Uw model registreren

Nu u hebt gecontroleerd of uw model werkt met Triton, registreert u dit bij Azure Machine Learning. Model registratie slaat uw model bestanden op in de werk ruimte Azure Machine Learning en wordt gebruikt bij de implementatie met de python-SDK en Azure CLI.

De volgende voor beelden laten zien hoe u de model (len) kunt registreren:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="verify-you-can-call-into-your-model"></a>Controleer of u het model kunt aanroepen

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

## <a name="redeploy-with-an-inference-configuration"></a>Opnieuw implementeren met een configuratie voor afnemen van een interferentie

Met een Afleidings configuratie kunt u een invoer script en het Azure Machine Learning-implementatie proces gebruiken met behulp van de python-SDK of Azure CLI.

> [!IMPORTANT]
> U moet de met de curator gevoerde `AzureML-Triton` [omgeving](./resource-curated-environments.md)opgeven.
>
> Het code voorbeeld van python wordt gekloond `AzureML-Triton` in een andere omgeving met de naam `My-Triton` . De Azure CLI-code gebruikt ook deze omgeving. Zie de referentie [environment. Clone ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) voor meer informatie over het klonen van een omgeving.

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

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!TIP]
> Zie voor meer informatie over het maken van een Afleidings configuratie het [schema](./reference-azure-machine-learning-cli.md#inference-configuration-schema)voor het afstellen van interferentie.

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

Nadat de implementatie is voltooid, wordt de Score-URI weer gegeven. Voor deze lokale implementatie is het `http://localhost:6789/score` . Als u in de Cloud implementeert, kunt u de opdracht [AZ ml service show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI gebruiken om de Score-URI op te halen.

Zie [een model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)voor meer informatie over het maken van een client die inactiviteits aanvragen verzendt naar de Score-URI.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent de Azure Machine Learning-werk ruimte te blijven gebruiken, maar de geïmplementeerde service wilt verwijderen, gebruikt u een van de volgende opties:

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>Volgende stappen

* [Bekijk end-to-end-voor beelden van Triton in Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Bekijk de [voor beelden van Triton-clients](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* Lees de [Triton-Server documentatie](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html) voor deservering
* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
