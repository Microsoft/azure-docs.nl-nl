---
title: Bestaande modellen gebruiken en implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw lokaal getrainde ML-modellen naar de Azure-Cloud kunt brengen met Azure Machine Learning.  U kunt modellen registreren die buiten Azure Machine Learning zijn gemaakt en deze vervolgens implementeren als webservice of Azure IoT Edge module.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c0d36ec5020f595d8a61899ddc8f8b9c282bad19
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328408"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Uw bestaande model implementeren met Azure Machine Learning


In dit artikel leert u hoe u een machine learning model dat u hebt getraind buiten Azure Machine Learning kunt registreren en implementeren. U kunt dit implementeren als een webservice of op een IoT Edge apparaat.  Na de implementatie kunt u uw model controleren en gegevens drift detecteren in Azure Machine Learning. 

Zie [machine learning modellen beheren, implementeren en bewaken](concept-model-management-and-deployment.md)voor meer informatie over de concepten en termen in dit artikel.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Machine Learning-werkruimte](how-to-manage-workspace.md)
  + Bij python-voorbeelden wordt ervan uitgegaan dat de `ws` variabele is ingesteld op uw Azure machine learning-werkruimte. Raadpleeg de [documentatie van Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#&preserve-view=trueworkspace) voor meer informatie over het maken van een verbinding met de werkruimte.
  
  + CLI-voorbeelden gebruiken tijdelijke aanduidingen van `myworkspace` en `myresourcegroup` , die u moet vervangen door de naam van uw werkruimte en de resource groep waarin deze is opgenomen.

* De [Azure machine learning python-SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).  

* De [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) -en [machine learning cli-extensie](reference-azure-machine-learning-cli.md).

* Een getraind model. Het model moet persistent zijn gemaakt voor een of meer bestanden in uw ontwikkelomgeving. <br><br>Ter illustratie van het registreren van een getraind model maakt de voorbeeldcode in dit artikel gebruik van de modellen van het [Paolo Ripamonti Twitter sentiment Analysis-project](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Model(len) registreren

Als u een model registreert, kunt u meta gegevens over modellen in uw werkruimte opslaan, en versies bijhouden. In de volgende python-en CLI-voorbeelden `models` bevat de map de `model.h5` bestanden, `model.w2v` `encoder.pkl` en `tokenizer.pkl` . In dit voorbeeld worden de bestanden in de `models` map geüpload als een nieuwe model registratie met de naam `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Zie voor meer informatie de referentie [model. register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) .

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> U kunt ook `tags` `properties` de objecten add en Dictionary instellen op het geregistreerde model. Deze waarden kunnen later worden gebruikt om een specifiek model te identificeren. Bijvoorbeeld het gebruikte Framework, de trainings parameters, enzovoort.

Zie voor meer informatie de verwijzing [AZ ml model REGI ster](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-register) .


Zie [machine learning modellen beheren, implementeren en bewaken](concept-model-management-and-deployment.md) voor meer informatie over model registratie in het algemeen.

## <a name="define-inference-configuration"></a>Definiëren van een inferentie configuratie

De inferentieconfiguratie definieert de omgeving die wordt gebruikt om het geïmplementeerde model uit te voeren. De inferentieconfiguratie verwijst naar de volgende entiteiten, die worden gebruikt om het model uit te voeren wanneer het wordt geïmplementeerd:

* Een invoer script, met de naam `score.py` , laadt het model wanneer de geïmplementeerde service wordt gestart. Dit script is ook verantwoordelijk voor het ontvangen van gegevens, het doorgeven aan het model en het retourneren van een antwoord.
* Een Azure Machine Learning [omgeving](how-to-use-environments.md). Een omgeving definieert de software afhankelijkheden die nodig zijn voor het uitvoeren van het model en het invoer script.

In het volgende voorbeeld ziet u hoe u de SDK gebruikt om een omgeving te maken en deze vervolgens te gebruiken met een inferentie configuratie:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Raadpleeg voor meer informatie de volgende artikelen:

+ [Het gebruik van omgevingen](how-to-use-environments.md).
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) -verwijzing.


De CLI laadt de configuratie van de inferentie uit een YAML-bestand:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Met de CLI wordt de Conda-omgeving gedefinieerd in het `myenv.yml` bestand waarnaar wordt verwezen door de inferentie configuratie. De volgende YAML is de inhoud van dit bestand:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md) voor meer informatie over het afnemen van de configuratie.

### <a name="entry-script-scorepy"></a>Invoer-script (score.py)

Het invoer script heeft slechts twee vereiste functies, `init()` en `run(data)` . Deze functies worden gebruikt voor het initialiseren van de service bij het opstarten en het uitvoeren van het model met behulp van aanvraag gegevens die door een client worden doorgegeven. De rest van het script zorgt voor het laden en uitvoeren van de model(len).

> [!IMPORTANT]
> Er is geen algemeen invoer script dat voor alle modellen werkt. Het is altijd specifiek voor het model dat wordt gebruikt. Het moet inzicht krijgen in het laden van het model, de gegevens indeling die het model verwacht en hoe u gegevens met behulp van het model kunt scoren.

De volgende python-code is een voorbeeld script ( `score.py` ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Zie [Modellen implementeren met Azure Machine Learning](how-to-deploy-and-where.md) voor meer informatie over invoerscripts.

## <a name="define-deployment"></a>Implementatie definiëren

Het [webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py&preserve-view=true) -pakket bevat de klassen die worden gebruikt voor de implementatie. De klasse die u gebruikt, bepaalt waar het model wordt geïmplementeerd. Als u bijvoorbeeld wilt implementeren als een webservice op de Azure Kubernetes-service, gebruikt u [AksWebService. deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) om de implementatie configuratie te maken.

De volgende python-code definieert een implementatie configuratie voor een lokale implementatie. Met deze configuratie wordt het model als een webservice geïmplementeerd op uw lokale computer.

> [!IMPORTANT]
> Een lokale implementatie vereist een werkende installatie van [docker](https://www.docker.com/) op uw lokale computer:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Zie de referentie [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-port-none-) voor meer informatie.

De CLI laadt de implementatie configuratie van een YAML-bestand:

```YAML
{
    "computeType": "LOCAL"
}
```

Om te implementeren op een ander rekendoel, zoals de Azure Kubernetes-service in de Azure-Cloud, is het voldoende om de implementatie configuratie te wijzigen. Zie [hoe en waar modellen worden geïmplementeerd](how-to-deploy-and-where.md) voor meer informatie.

## <a name="deploy-the-model"></a>Het model implementeren

In het volgende voorbeeld wordt informatie over het geregistreerde model geladen `sentiment` en vervolgens geïmplementeerd als een service met de naam `sentiment` . Tijdens de implementatie worden de configuratie-en implementatie configuratie voor de inferenties gebruikt om de service omgeving te maken en te configureren:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Zie de naslag gids voor [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) voor meer informatie.

Gebruik de volgende opdracht om het model te implementeren vanuit de CLI. Met deze opdracht wordt versie 1 van het geregistreerde model ( `sentiment:1` ) geïmplementeerd met behulp van de inferentie en implementatie configuratie die is opgeslagen in de `inferenceConfig.json` `deploymentConfig.json` bestanden en:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Zie voor meer informatie de referentie [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy) .

Zie [hoe en wanneer u modellen implementeert](how-to-deploy-and-where.md) voor meer informatie over de implementatie.

## <a name="request-response-consumption"></a>Aanvraag-antwoord verbruik

Na de implementatie wordt de Score-URI weer gegeven. Deze URI kan worden gebruikt door clients voor het verzenden van aanvragen naar de service. Het volgende voorbeeld is een eenvoudige python-client waarmee gegevens naar de service worden verzonden en het antwoord wordt weergegeven:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Zie [een client maken](how-to-consume-web-service.md) voor meer informatie over het gebruiken van de geïmplementeerde service.

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Een client voor een geïmplementeerd model maken](how-to-consume-web-service.md)
