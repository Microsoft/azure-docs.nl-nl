---
title: Bestaande modellen gebruiken en implementeren
titleSuffix: Azure Machine Learning
description: Leer hoe u ML-modellen die buiten Azure zijn getraind, naar de Azure-cloud kunt brengen met Azure Machine Learning. Implementeer het model vervolgens als een webservice of IoT-module.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: how-to
ms.custom: devx-track-python, deploy
ms.openlocfilehash: 651e158009c87449bef339d131edbb188763b45f
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885448"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Uw bestaande model implementeren met Azure Machine Learning


In dit artikel leert u hoe u een model registreert en implementeert machine learning dat u buiten uw Azure Machine Learning. U kunt implementeren als een webservice of op een IoT Edge apparaat.  Zodra het model is geïmplementeerd, kunt u uw model bewaken en gegevensdrift in de Azure Machine Learning. 

Zie [Manage, deploy,](concept-model-management-and-deployment.md)and monitor machine learning models (Modellen beheren, implementeren en bewaken) voor meer informatie over de concepten en termen in machine learning artikel.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Machine Learning werkruimte](how-to-manage-workspace.md)
  + Python-voorbeelden gaan ervan uit `ws` dat de variabele is ingesteld op Azure Machine Learning werkruimte. Raadpleeg de documentatie over de Azure Machine Learning [SDK voor Python](/python/api/overview/azure/ml/#workspace)voor meer informatie over het maken van verbinding met de werkruimte.
  
  + CLI-voorbeelden gebruiken tijdelijke aanduidingen van en , die u moet vervangen door de naam van uw werkruimte en `myworkspace` `myresourcegroup` de resourcegroep die deze bevat.

* De [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install).  

* De [Azure CLI en](/cli/azure/install-azure-cli) Machine Learning [CLI-extensie](reference-azure-machine-learning-cli.md).

* Een getraind model. Het model moet worden opgeslagen in een of meer bestanden in uw ontwikkelomgeving. <br><br>Ter demonstratie van het registreren van een model dat is getraind, maakt de voorbeeldcode in dit artikel gebruik van de modellen van [het Twitter-sentimentanalyseproject van Zullen Ripamonti.](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)

## <a name="register-the-models"></a>Het model(en) registreren

Als u een model registreert, kunt u metagegevens over modellen opslaan, versien en bijhouden in uw werkruimte. In de volgende Python- en CLI-voorbeelden bevat `models` de map de bestanden , , en `model.h5` `model.w2v` `encoder.pkl` `tokenizer.pkl` . In dit voorbeeld worden de bestanden in de `models` map geüpload als een nieuwe modelregistratie met de naam `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Zie de naslaginformatie over [Model.register() voor meer](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) informatie.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> U kunt ook objecten toevoegen `tags` en `properties` woordenlijst instellen op het geregistreerde model. Deze waarden kunnen later worden gebruikt om een specifiek model te identificeren. Bijvoorbeeld het gebruikte framework, trainingsparameters, enzovoort.

Zie de naslaginformatie over [az ml model register voor meer](/cli/azure/ml/model#az_ml_model_register) informatie.


Zie [Manage, deploy, and monitor machine learning models](concept-model-management-and-deployment.md)(Modellen beheren, implementeren en bewaken) voor meer informatie over modelregistratie in machine learning algemeen.

## <a name="define-inference-configuration"></a>De deferentieconfiguratie definiëren

De deferentieconfiguratie definieert de omgeving die wordt gebruikt om het geïmplementeerde model uit te voeren. De deferentieconfiguratie verwijst naar de volgende entiteiten, die worden gebruikt om het model uit te voeren wanneer het wordt geïmplementeerd:

* Een invoerscript met de `score.py` naam laadt het model wanneer de geïmplementeerde service wordt gestart. Dit script is ook verantwoordelijk voor het ontvangen van gegevens, het doorgeven ervan aan het model en het retourneren van een antwoord.
* Een [Azure Machine Learning-omgeving.](how-to-use-environments.md) Een omgeving definieert de softwareafhankelijkheden die nodig zijn om het model en invoerscript uit te voeren.

In het volgende voorbeeld ziet u hoe u de SDK gebruikt om een omgeving te maken en deze vervolgens te gebruiken met een deference-configuratie:

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

+ [Omgevingen gebruiken.](how-to-use-environments.md)
+ [Naslag voor InferenceConfig.](/python/api/azureml-core/azureml.core.model.inferenceconfig)


De CLI laadt de deference-configuratie uit een YAML-bestand:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Met de CLI wordt de Conda-omgeving gedefinieerd in het `myenv.yml` bestand waarnaar wordt verwezen door de deference-configuratie. De volgende YAML is de inhoud van dit bestand:

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

Zie Modellen implementeren met Azure Machine Learning voor meer informatie [over de deferentieconfiguratie.](how-to-deploy-and-where.md)

### <a name="entry-script-scorepy"></a>Invoerscript (score.py)

Het invoerscript heeft slechts twee vereiste functies, `init()` en `run(data)` . Deze functies worden gebruikt om de service bij het opstarten te initialiseren en het model uit te voeren met behulp van aanvraaggegevens die zijn doorgegeven door een client. De rest van het script verwerkt het laden en uitvoeren van de model(s).

> [!IMPORTANT]
> Er is geen algemeen invoerscript dat voor alle modellen werkt. Het is altijd specifiek voor het model dat wordt gebruikt. Het moet weten hoe het model moet worden geladen, de gegevensindeling die het model verwacht en hoe gegevens kunnen worden gescored met behulp van het model.

De volgende Python-code is een voorbeeld van een invoerscript ( `score.py` ):

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

Het [webservicepakket](/python/api/azureml-core/azureml.core.webservice) bevat de klassen die worden gebruikt voor implementatie. De klasse die u gebruikt, bepaalt waar het model wordt geïmplementeerd. Als u bijvoorbeeld wilt implementeren als een webservice op Azure Kubernetes Service, gebruikt [u AksWebService.deploy_configuration() om](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) de implementatieconfiguratie te maken.

De volgende Python-code definieert een implementatieconfiguratie voor een lokale implementatie. Met deze configuratie wordt het model als een webservice op uw lokale computer geïmplementeerd.

> [!IMPORTANT]
> Voor een lokale implementatie is een werkende installatie van [Docker](https://www.docker.com/) op uw lokale computer vereist:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Zie de naslaginformatie over [LocalWebservice.deploy_configuration() voor meer](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-) informatie.

De CLI laadt de implementatieconfiguratie vanuit een YAML-bestand:

```YAML
{
    "computeType": "LOCAL"
}
```

Implementeren naar een ander rekendoel, zoals Azure Kubernetes Service in de Azure-cloud, is net zo eenvoudig als het wijzigen van de implementatieconfiguratie. Zie How [and where to deploy models (Modellen implementeren) voor meer informatie.](how-to-deploy-and-where.md)

## <a name="deploy-the-model"></a>Het model implementeren

In het volgende voorbeeld wordt informatie over het geregistreerde model met de naam geladen en vervolgens `sentiment` geïmplementeerd als een service met de naam `sentiment` . Tijdens de implementatie worden de deferentieconfiguratie en implementatieconfiguratie gebruikt om de serviceomgeving te maken en te configureren:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Zie de naslaginformatie over [Model.deploy() voor meer](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) informatie.

Gebruik de volgende opdracht om het model te implementeren vanuit de CLI. Met deze opdracht wordt versie 1 van het geregistreerde model ( ) geïmplementeerd met behulp van de deference- en implementatieconfiguratie die is opgeslagen `sentiment:1` in de bestanden en `inferenceConfig.json` `deploymentConfig.json` :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Zie de naslaginformatie over [az ml model deploy voor meer](/cli/azure/ml/model#az_ml_model_deploy) informatie.

Zie How [and where to deploy models (Modellen implementeren) voor meer informatie over implementatie.](how-to-deploy-and-where.md)

## <a name="request-response-consumption"></a>Verbruik van aanvraag-antwoord

Na de implementatie wordt de scoring-URI weergegeven. Deze URI kan door clients worden gebruikt om aanvragen naar de service te verzenden. Het volgende voorbeeld is een eenvoudige Python-client die gegevens naar de service verstuurt en het antwoord we geven:

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

Zie Een client maken voor meer informatie over het gebruik van de [geïmplementeerde service.](how-to-consume-web-service.md)

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Machine Learning bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Een client maken voor een geïmplementeerd model](how-to-consume-web-service.md)