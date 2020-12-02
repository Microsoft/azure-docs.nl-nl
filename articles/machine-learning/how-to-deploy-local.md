---
title: Lokaal uitvoeren en implementeren
titleSuffix: Azure Machine Learning
description: In dit artikel wordt beschreven hoe u uw lokale computer als doel gebruikt voor training, fout opsporing of het implementeren van modellen die zijn gemaakt in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 1d2e25f76d9a68eeb01a45c34651fe1537297980
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510570"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>Implementeren op uw lokale computers-modellen die zijn getraind met Azure Machine Learning

In dit artikel wordt beschreven hoe u uw lokale computer gebruikt als doel voor het trainen of implementeren van modellen die zijn gemaakt in Azure Machine Learning. Azure Machine Learning is flexibel genoeg om met de meeste python machine learning Frameworks te werken. Machine Learning-oplossingen hebben doorgaans complexe afhankelijkheden die moeilijk te dupliceren zijn. In dit artikel wordt uitgelegd hoe u het totale beheer kunt verdelen met gebruiks gemak.

Scenario's voor lokale implementatie zijn onder andere:

* Snel iteratieen van gegevens, scripts en modellen in een project.
* Fout opsporing en probleem oplossing in latere fasen.
* Definitieve implementatie op door de gebruiker beheerde hardware.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.
- Een model en een omgeving. Als u geen getraind model hebt, kunt u het model en de afhankelijkheids bestanden van [deze zelf studie](tutorial-train-models-with-aml.md)gebruiken.
- De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
- Een Conda Manager, zoals Anaconda of Miniconda, als u Azure Machine Learning pakket afhankelijkheden wilt spie gelen.
- Docker: als u een container versie van de Azure Machine Learning omgeving wilt gebruiken.

## <a name="prepare-your-local-machine"></a>Uw lokale machine voorbereiden

De meest betrouw bare manier om een Azure Machine Learning model lokaal uit te voeren, is met een docker-installatie kopie. Een docker-installatie kopie biedt een geïsoleerde, in containers geplaatste ervaring, met uitzonde ring van hardwareproblemen, de Azure Execution Environment. Zie [overzicht van docker Remote Development in Windows](/windows/dev-environment/docker/overview)voor meer informatie over het installeren en configureren van docker voor ontwikkelings scenario's.

Het is mogelijk om een fout opsporingsprogramma te koppelen aan een proces dat wordt uitgevoerd in docker. (Zie [koppelen aan een actieve container](https://code.visualstudio.com/docs/remote/attach-container).) U kunt de python-code echter beter debuggen en herhalen zonder docker. In dit scenario is het belang rijk dat uw lokale computer dezelfde bibliotheken gebruikt die worden gebruikt bij het uitvoeren van uw experiment in Azure Machine Learning. Azure gebruikt [Conda](https://docs.conda.io/)om python-afhankelijkheden te beheren. U kunt de omgeving opnieuw maken met behulp van andere pakket beheerders, maar het installeren en configureren van Conda op de lokale computer is de eenvoudigste manier om te synchroniseren. 

## <a name="prepare-your-entry-script"></a>Het script voor de invoer voorbereiden

Zelfs als u docker gebruikt om het model en de afhankelijkheden te beheren, moet het python-Score script lokaal zijn. Het script moet twee methoden hebben:

- Een `init()` methode die geen argumenten accepteert en niets retourneert 
- Een `run()` methode waarbij een JSON-indelings teken reeks wordt gebruikt en een JSON-serialiseerbaar object wordt geretourneerd

Het argument voor de `run()` methode heeft de volgende vorm: 

```json
{
    "data": <model-specific-data-structure>
}
```

Het object dat u van de `run()` methode retourneert, moet worden geïmplementeerd `toJSON() -> string` .

In het volgende voor beeld wordt gedemonstreerd hoe u een geregistreerd scikit-model kunt laden en een score hebt gemaakt met behulp van NumPy-gegevens. Dit voor beeld is gebaseerd op het model en de afhankelijkheden van [deze zelf studie](tutorial-train-models-with-aml.md).

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

Voor meer geavanceerde voor beelden, waaronder automatisch genereren van Swagger-schema's en het berekenen van binaire gegevens (bijvoorbeeld afbeeldingen), raadpleegt u [geavanceerde invoer scripts ontwerpen](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Implementeren als een lokale webservice met behulp van docker

De gemakkelijkste manier om de omgeving te repliceren die wordt gebruikt door Azure Machine Learning is een webservice te implementeren met behulp van docker. Als docker op uw lokale machine wordt uitgevoerd, doet u het volgende:

1. Maak verbinding met de Azure Machine Learning-werk ruimte waarin uw model is geregistreerd.
1. Maak een- `Model` object dat het model vertegenwoordigt.
1. Maak een `Environment` object dat de afhankelijkheden bevat en definieert de software omgeving waarin uw code wordt uitgevoerd.
1. Maak een `InferenceConfig` object dat het item script koppelt aan de `Environment` .
1. Maak een `DeploymentConfiguration` object van de subklasse `LocalWebserviceDeploymentConfiguration` .
1. Gebruiken `Model.deploy()` om een- `Webservice` object te maken. Met deze methode wordt de docker-installatie kopie gedownload en gekoppeld aan de `Model` , `InferenceConfig` , en `DeploymentConfiguration` .
1. Activeer de `Webservice` via `Webservice.wait_for_deployment()` .

De volgende code toont deze stappen:

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

De aanroep naar `Model.deploy()` kan een paar minuten duren. Nadat u de webservice voor het eerst hebt geïmplementeerd, is het efficiënter om de methode te gebruiken `update()` in plaats van helemaal vanaf het begin te beginnen. Zie [een geïmplementeerde webservice bijwerken](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Uw lokale implementatie testen

Wanneer u het vorige implementatie script uitvoert, wordt de URI uitgevoerd waarnaar u gegevens voor score kunt plaatsen (bijvoorbeeld `http://localhost:6789/score` ). In het volgende voor beeld ziet u een script waarin de voorbeeld gegevens worden gescoord met behulp van het `"sklearn-mnist-local"` lokaal geïmplementeerde model. Het model wordt, indien het goed is getraind, Afgeleidingen die `normalized_pixel_values` moeten worden geïnterpreteerd als een "2". 

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

## <a name="download-and-run-your-model-directly"></a>Uw model direct downloaden en uitvoeren

Het gebruik van docker om uw model te implementeren als een webservice is de meest voorkomende optie. Maar misschien wilt u uw code rechtstreeks uitvoeren met behulp van lokale python-scripts. U hebt twee belang rijke onderdelen nodig: 

- Het model zelf
- De afhankelijkheden waarop het model is gebaseerd 

U kunt het model downloaden:  

- Selecteer in de portal, door het tabblad **modellen** te selecteren, het gewenste model te selecteren en selecteer op de pagina **Details** de optie **downloaden**.
- Vanaf de opdracht regel met behulp van `az ml model download` . (Zie [model downloaden.](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false))
- Met behulp van de python-SDK- `Model.download()` methode. (Zie [model klasse.](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false))

Een Azure-model is een of meer geserialiseerde python-objecten, verpakt als een python-selectie bestand (extensie. PKL). De inhoud van het selectie bestand is afhankelijk van de machine learning-bibliotheek of-techniek die wordt gebruikt om het model te trainen. Als u bijvoorbeeld het model uit de zelf studie gebruikt, kunt u het model laden met:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Afhankelijkheden zijn altijd lastig om recht te krijgen, met name met machine learning, waar er vaak een dizzying-Web van specifieke versie vereisten kan zijn. U kunt een Azure Machine Learning omgeving opnieuw maken op uw lokale computer als een volledige Conda-omgeving of als docker-installatie kopie met behulp `build_local()` van de methode van de- `Environment` klasse: 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Als u het `build_local()` `useDocker` argument instelt op `True` , wordt met de functie een docker-installatie kopie gemaakt in plaats van een Conda-omgeving. Als u meer controle wilt, kunt u de `save_to_directory()` methode van gebruiken `Environment` . Hiermee schrijft u conda_dependencies. yml en azureml_environment.jsop definitie bestanden die u kunt afstemmen en gebruiken als basis voor uitbrei ding. 

De `Environment` klasse heeft een aantal andere methoden voor het synchroniseren van omgevingen in uw Compute-hardware, uw Azure-werk ruimte en docker-installatie kopieën. Zie [omgevings klasse](/python/api/azureml-core/azureml.core.environment(class))voor meer informatie.

Nadat u het model hebt gedownload en de afhankelijkheden hebt opgelost, zijn er geen door Azure gedefinieerde beperkingen voor het uitvoeren van scores, het verfijnen van het model, het gebruik van overboeking Learning, enzovoort. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Een opnieuw getraind model uploaden naar Azure Machine Learning

Als u een lokaal getraind of een opnieuw getraind model hebt, kunt u dit registreren bij Azure. Nadat de registratie is geregistreerd, kunt u deze door lopen met behulp van Azure Compute of implementeren met behulp van Azure-functies, zoals de [Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md) of de Triton-Afleidings [Server (preview)](how-to-deploy-with-triton.md).

Een model moet worden opgeslagen als een geserialiseerd python-object in de indeling (een. PKL-bestand) om te worden gebruikt met de python-SDK van Azure Machine Learning. Ook moet er een methode worden geïmplementeerd `predict(data)` die een JSON-serialiseerbaar object retourneert. U kunt bijvoorbeeld een lokaal getraind scikit-leer diabetes-model opslaan met: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Als u het model beschikbaar wilt maken in azure, kunt u de `register()` methode van de- `Model` klasse gebruiken:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

U kunt vervolgens het nieuwe geregistreerde model vinden op het tabblad Azure Machine Learning **model** :

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Scherm afbeelding van het tabblad Azure Machine Learning model, waarin een geüpload model wordt weer gegeven.":::

Zie voor meer informatie over het uploaden en bijwerken van modellen en omgevingen [model registreren en lokaal implementeren met geavanceerde gebruiks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)gegevens.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het beheren van omgevingen [& software omgevingen maken gebruiken in azure machine learning](how-to-use-environments.md).
- Zie [verbinding maken met Storage services op Azure](how-to-access-data.md)voor meer informatie over het openen van gegevens uit uw Data Store.
