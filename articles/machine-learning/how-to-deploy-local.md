---
title: Lokaal uitvoeren en implementeren
titleSuffix: Azure Machine Learning
description: In dit artikel wordt beschreven hoe u uw lokale computer gebruikt als doel voor het trainen, debuggen of implementeren van modellen die zijn gemaakt in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 75836580fc2dc5a2090047865610e26d856387b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861208"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Modellen implementeren die zijn getraind met Azure Machine Learning op uw lokale machines 

In dit artikel wordt beschreven hoe u uw lokale computer gebruikt als doel voor het trainen of implementeren van modellen die zijn gemaakt in Azure Machine Learning. Azure Machine Learning is flexibel genoeg om te werken met de meeste Python machine learning frameworks. Machine learning-oplossingen hebben doorgaans complexe afhankelijkheden die moeilijk te dupliceren zijn. In dit artikel wordt beschreven hoe u een goede balans kunt vinden tussen totale controle en gebruiksgemak.

Scenario's voor lokale implementatie zijn onder andere:

* Snel gegevens, scripts en modellen vroeg in een project itereren.
* Foutopsporing en probleemoplossing in latere fasen.
* Definitieve implementatie op door de gebruiker beheerde hardware.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)
- Een model en een omgeving. Als u geen getraind model hebt, kunt u het model en de afhankelijkheidsbestanden in deze [zelfstudie gebruiken.](tutorial-train-models-with-aml.md)
- De [Azure Machine Learning-SDK voor Python](/python/api/overview/azure/ml/intro).
- Een Conda-manager, zoals Anaconda of Miniconda, als u afhankelijkheden van Azure Machine Learning wilt spiegelen.
- Docker, als u een in een container geplaatste versie van de Azure Machine Learning gebruiken.

## <a name="prepare-your-local-machine"></a>Uw lokale computer voorbereiden

De betrouwbaarste manier om lokaal een Azure Machine Learning uit te voeren, is met een Docker-afbeelding. Een Docker-afbeelding biedt een geïsoleerde, in een container geplaatste ervaring die de Azure-uitvoeringsomgeving dupliceert, met uitzondering van hardwareproblemen. Zie Overzicht van externe Docker-ontwikkeling in Windows voor meer informatie over het installeren en configureren van Docker [voor ontwikkelingsscenario's.](/windows/dev-environment/docker/overview)

Het is mogelijk om een debugger te koppelen aan een proces dat wordt uitgevoerd in Docker. (Zie [Koppelen aan een container die wordt uitgevoerd.)](https://code.visualstudio.com/docs/remote/attach-container) Maar mogelijk wilt u liever fouten opsporen en uw Python-code itereren zonder docker te gebruiken. In dit scenario is het belangrijk dat uw lokale computer dezelfde bibliotheken gebruikt die worden gebruikt wanneer u uw experiment in Azure Machine Learning. Voor het beheren van Python-afhankelijkheden gebruikt Azure [conda](https://docs.conda.io/). U kunt de omgeving opnieuw maken met behulp van andere pakketmanagers, maar het installeren en configureren van conda op uw lokale computer is de eenvoudigste manier om te synchroniseren. 

## <a name="prepare-your-entry-script"></a>Uw invoerscript voorbereiden

Zelfs als u Docker gebruikt om het model en de afhankelijkheden te beheren, moet het Python-scorescript lokaal zijn. Het script moet twee methoden hebben:

- Een `init()` methode die geen argumenten gebruikt en niets retourneert 
- Een `run()` methode die een tekenreeks in JSON-indeling gebruikt en een met JSON serializeerbaar object retourneert

Het argument voor de `run()` methode heeft deze vorm: 

```json
{
    "data": <model-specific-data-structure>
}
```

Het object dat u retourneert van `run()` de methode moet `toJSON() -> string` implementeren.

In het volgende voorbeeld wordt gedemonstreerd hoe u een geregistreerd scikit-learn-model laadt en een score geeft met behulp van NumPy-gegevens. Dit voorbeeld is gebaseerd op het model en de afhankelijkheden van [deze zelfstudie.](tutorial-train-models-with-aml.md)

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Zie Geavanceerde invoerscripts schrijven voor meer geavanceerde voorbeelden, waaronder het automatisch genereren van Swagger-schema's en het scoren van binaire gegevens (bijvoorbeeld [afbeeldingen).](how-to-deploy-advanced-entry-script.md) 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Implementeren als een lokale webservice met behulp van Docker

De eenvoudigste manier om de omgeving te repliceren die wordt gebruikt door Azure Machine Learning is door een webservice te implementeren met behulp van Docker. Als Docker wordt uitgevoerd op uw lokale computer, doet u het volgende:

1. Maak verbinding met Azure Machine Learning werkruimte waarin uw model is geregistreerd.
1. Maak een `Model` -object dat het model vertegenwoordigt.
1. Maak een `Environment` -object dat de afhankelijkheden bevat en definieert de softwareomgeving waarin uw code wordt uitgevoerd.
1. Maak een `InferenceConfig` -object dat het invoerscript koppelt aan de `Environment` .
1. Maak een `DeploymentConfiguration` -object van de subklasse `LocalWebserviceDeploymentConfiguration` .
1. Gebruik `Model.deploy()` om een -object te `Webservice` maken. Met deze methode downloadt u de Docker-afbeelding en koppelt u deze aan `Model` `InferenceConfig` de , en `DeploymentConfiguration` .
1. Activeer `Webservice` de met behulp van `Webservice.wait_for_deployment()` .

In de volgende code ziet u deze stappen:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

De aanroep `Model.deploy()` naar kan enkele minuten duren. Nadat u de webservice voor het eerst hebt geïmplementeerd, is het efficiënter om de methode te gebruiken `update()` in plaats van opnieuw te beginnen. Zie [Een geïmplementeerde webservice bijwerken.](how-to-deploy-update-web-service.md)

### <a name="test-your-local-deployment"></a>Uw lokale implementatie testen

Wanneer u het vorige implementatiescript hebt uitgevoerd, wordt de URI uitgevoerd waarop u gegevens kunt plaatsen om te scoren (bijvoorbeeld `http://localhost:6789/score` ). In het volgende voorbeeld ziet u een script dat voorbeeldgegevens scoort met behulp van `"sklearn-mnist-local"` het lokaal geïmplementeerde model. Het model, indien goed getraind, defeert dat `normalized_pixel_values` moet worden geïnterpreteerd als een '2'. 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Uw model rechtstreeks downloaden en uitvoeren

Het gebruik van Docker om uw model te implementeren als een webservice is de meest voorkomende optie. Maar misschien wilt u uw code rechtstreeks uitvoeren met behulp van lokale Python-scripts. U hebt twee belangrijke onderdelen nodig: 

- Het model zelf
- De afhankelijkheden waarvan het model afhankelijk is 

U kunt het model downloaden:  

- Selecteer in de portal het tabblad **Modellen,** selecteer het gewenste model en selecteer op de pagina **Details** de optie **Downloaden.**
- Vanaf de opdrachtregel met behulp van `az ml model download` . (Zie [Model downloaden.](/cli/azure/ml/model#az_ml_model_download))
- Met behulp van de Python `Model.download()` SDK-methode. (Zie [Modelklasse.](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-))

Een Azure-model is een of meer geseraliseerde Python-objecten, verpakt als een Python pickle-bestand (.pkl-extensie). De inhoud van het pickle-bestand is afhankelijk van de machine learning of techniek die wordt gebruikt om het model te trainen. Als u bijvoorbeeld het model uit de zelfstudie gebruikt, kunt u het model laden met:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Afhankelijkheden zijn altijd lastig om het goed te krijgen, met name met machine learning, waarbij er vaak een web met specifieke versievereisten kan zijn. U kunt een Azure Machine Learning opnieuw maken op uw lokale computer als een volledige Conda-omgeving of als een Docker-afbeelding met behulp van de methode `build_local()` van de `Environment` klasse : 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Als u het `build_local()` `useDocker` argument in stelt op , maakt de `True` functie een Docker-afbeelding in plaats van een Conda-omgeving. Als u meer controle wilt, kunt u de methode gebruiken van , die `save_to_directory()` conda_dependencies.yml en azureml_environment.jsschrijft op definitiebestanden die u kunt afstemmen en gebruiken als basis voor `Environment` extensies. 

De klasse heeft een aantal andere methoden voor het synchroniseren van omgevingen in uw `Environment` rekenhardware, uw Azure-werkruimte en Docker-afbeeldingen. Zie [Omgevingsklasse voor meer informatie.](/python/api/azureml-core/azureml.core.environment(class))

Nadat u het model hebt gedownload en de afhankelijkheden ervan hebt opgelost, zijn er geen door Azure gedefinieerde beperkingen voor het uitvoeren van scores, het afstemmen van het model, het gebruik van leeroverdracht, enzovoort. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Een opnieuw getraind model uploaden naar Azure Machine Learning

Als u een lokaal getraind of opnieuw getraind model hebt, kunt u het registreren bij Azure. Nadat deze is geregistreerd, kunt u doorgaan met het afstemmen met behulp van Azure Compute of implementeren met behulp van Azure-faciliteiten zoals [Azure Kubernetes Service of](how-to-deploy-azure-kubernetes-service.md) [Triton Inference Server (preview).](how-to-deploy-with-triton.md)

Om te worden gebruikt met de Azure Machine Learning Python SDK, moet een model worden opgeslagen als een geseraliseerd Python-object in pickle-indeling (een .pkl-bestand). Er moet ook een methode `predict(data)` worden geïmplementeerd die een JSON-serializeerbaar object retourneert. U kunt bijvoorbeeld een lokaal getraind scikit-learn-diabetesmodel opslaan met: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Als u het model beschikbaar wilt maken in Azure, kunt u vervolgens de `register()` methode van de klasse `Model` gebruiken:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

U vindt uw zojuist geregistreerde model vervolgens op het tabblad Azure Machine Learning **Model:**

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Schermopname van Azure Machine Learning tabblad Model, met een geüpload model.":::

Zie Model registreren en lokaal implementeren met geavanceerd gebruik voor meer informatie over het uploaden en bijwerken van modellen [en omgevingen.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)

## <a name="next-steps"></a>Volgende stappen

- Zie Create & use software environments in Azure Machine Learning [(Softwareomgevingen](how-to-use-environments.md)gebruiken in Azure Machine Learning) voor meer informatie over het beheren Azure Machine Learning.
- Zie Verbinding maken met opslagservices in Azure voor meer informatie over het openen van [gegevens uit uw gegevensopslag.](how-to-access-data.md)
