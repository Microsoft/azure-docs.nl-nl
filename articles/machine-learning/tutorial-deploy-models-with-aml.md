---
title: 'Zelfstudie over de classificatie van afbeeldingen: Modellen implementeren'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie ziet u hoe u Azure Machine Learning kunt gebruiken om een afbeeldingsclassificatiemodel met scikit-learn in een Python Jupyter-notebook te implementeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 0981d325b3c5982793ada480a87afc48bf58acf7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066575"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Zelfstudie: Een afbeeldingsclassificatiemodel implementeren in Azure Container Instances


Deze zelfstudie is **deel twee van een tweedelige reeks**. In de [vorige zelfstudie](tutorial-train-models-with-aml.md) hebt u Machine Learning-modellen getraind en vervolgens een model geregistreerd in uw werkruimte in de cloud.  U bent nu klaar om het model te implementeren als een webservice. Een webservice is een installatiekopie, in dit geval een Docker-installatiekopie. De service omvat de scoringlogica en het model zelf. 

In dit gedeelte van de zelfstudie gebruikt u Azure Machine Learning voor de volgende taken:

> [!div class="checklist"]
> * Uw testomgeving instellen
> * Het model ophalen uit uw werkruimte
> * Het model implementeren in Container Instances
> * Het geïmplementeerde model testen

Container Instances is een uitstekende oplossing voor het testen en inzicht krijgen in de werkstroom. Voor schaalbare productie-implementaties is het misschien beter om Azure Kubernetes Service te gebruiken. Zie [Modellen implementeren met de Azure Machine Learning-service](how-to-deploy-and-where.md) voor meer informatie.

>[!NOTE]
> Code in dit artikel is getest met versie 1.0.83 van de Azure Machine Learning SDK.

## <a name="prerequisites"></a>Vereisten

Als u het notebook wilt uitvoeren, moet u eerst het trainen van het model voltooien in [Zelfstudie (deel 1): Een model voor de classificatie van afbeeldingen trainen](tutorial-train-models-with-aml.md).   Open in uw gekloonde map *tutorials/image-classification-mnist-data* de notebook *mg-classification-part2-deploy.ipynb*.

Deze zelfstudie is ook beschikbaar op [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) als u deze wilt uitvoeren in uw eigen [lokale omgeving](how-to-configure-environment.md#local).  Zorg ervoor dat u `matplotlib` en `scikit-learn` in uw omgeving hebt geïnstalleerd. 

> [!Important]
> De rest van dit artikel bevat dezelfde inhoud als die u ziet in de notebook.  
>
> Schakel nu over naar de Jupyter-notebook als u wilt meelezen tijdens het uitvoeren van de code.
> Als u één codecel in een notebook wilt uitvoeren, klikt u op de codecel en drukt u op **Shift+Enter**. U kunt ook de hele notebook uitvoeren door **Alle uitvoeren** te kiezen op de bovenste werkbalk.

## <a name="set-up-the-environment"></a><a name="start"></a>Stel de omgeving in

Begin met het instellen van een testomgeving.

### <a name="import-packages"></a>Pakketten importeren

Importeer de Python-pakketten die nodig zijn voor deze zelfstudie.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Als webservice implementeren

Implementeer het model als een webservice die wordt gehost in ACI. 

Geef de volgende zaken op om de juiste omgeving te maken voor ACI:
* Een scoring-script dat aangeeft hoe het model moet worden gebruikt
* Een configuratiebestand om de ACI mee te bouwen
* Het model dat u eerder hebt getraind

### <a name="create-scoring-script"></a>Scoring-script maken

Maak een scoring-script, met de naam score.py, dat door de aanroep van de webservice wordt gebruikt om aan te geven hoe het model moet worden gebruikt.

Er zijn twee functies die u verplicht in het scoring-script moet opnemen:
* De functie `init()`, die het model doorgaans in een algemeen object laadt. Deze functie wordt slechts één keer uitgevoerd wanneer de Docker-container wordt gestart. 

* De functie `run(input_data)` gebruikt het model om een waarde te voorspellen op basis van de invoergegevens. De in- en uitvoer van de uitvoerbewerking maken doorgaans gebruik van JSON voor serialisatie en deserialisatie, maar andere indelingen worden ook ondersteund.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>Een configuratiebestand maken

Maak een configuratiebestand voor de implementatie en geef het aantal CPU's en het aantal gigabytes aan RAM-geheugen op dat nodig is voor uw ACI-container. Het verschilt per model, maar voor de meeste modellen is de standaardhoeveelheid van 1 kerngeheugen en 1 gigabyte aan RAM-geheugen wel voldoende. Als u er later meer nodig hebt, moet u de installatiekopie opnieuw maken en de service opnieuw implementeren.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Implementeren in ACI
Geschatte duur: **ongeveer 2 tot 5 minuten**

Configureer en implementeer de installatiekopie. De volgende code doorloopt de volgende stappen:

1. Maak een omgevingsobject met afhankelijkheden die nodig zijn voor het model met behulp van de omgeving (`tutorial-env`) die is opgeslagen tijdens de training.
1. Maak de deductieconfiguratie die nodig is voor het implementeren van het model als een webservice met behulp van:
   * Het scoring-bestand (`score.py`)
   * het omgevingsobject dat u in de vorige stap hebt gemaakt
1. Implementeer het model in de ACI-container.
1. Haal het HTTP-eindpunt van de webservice op.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Haal het HTTP-eindpunt van de scoring-webservice op, die REST-clientaanroepen accepteert. Dit eindpunt kan worden gedeeld met iedereen die de webservice wil testen of wil integreren in een toepassing.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>Het model testen


### <a name="download-test-data"></a>Testgegevens downloaden
De testgegevens naar de map **./data/** downloaden


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Testgegevens laden

Laad de testgegevens uit de map **. /data/** , die u hebt gemaakt tijdens de zelfstudie over het trainen van modellen.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>Testgegevens voorspellen

Voer de testgegevensset in het model in om voorspellingen te krijgen.


De volgende code doorloopt de volgende stappen:
1. Verzend de gegevens als JSON-matrix naar de webservice die wordt gehost in ACI. 

1. Gebruik de API `run` van de SDK om de service aan te roepen. U kunt ook onbewerkte aanroepen doen met behulp van een HTTP-hulpprogramma zoals curl.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>De verwarringsmatrix bestuderen

Genereer een verwarringsmatrix om te zien hoeveel voorbeelden uit de testset juist zijn geclassificeerd. Let op de verkeerd ingedeelde waarden voor de onjuiste voorspellingen.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

In de uitvoer wordt de verwarringsmatrix weergegeven:

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

Gebruik `matplotlib` om de verwarringsmatrix weer te geven in een grafiek. In deze grafiek staat de X-as voor de daadwerkelijke waarden en de Y-as voor de voorspelde waarden. De kleur van elke cel staat voor de foutfrequentie. Hoe lichter de kleur, hoe hoger de foutfrequentie. De 5 is vaak verkeerd ingedeeld als een 3. Daarom ziet u een lichtgekleurd raster bij (5,3).

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Grafiek met een verwarringsmatrix](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>Voorspellingen weergeven

Test het geïmplementeerde model met een steekproef van 30 afbeeldingen uit de testgegevens.  


1. Druk de geretourneerde voorspellingen af en geef die weer samen met de invoerafbeeldingen. De verkeerd geclassificeerde voorbeelden worden aangegeven met een rood lettertype en inversie (wit op zwart). 

 Aangezien de nauwkeurigheid van het model hoog is, kan het nodig zijn om de volgende code een paar keer uit te voeren voordat u een voorbeeld van een onjuiste classificatie ziet.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

U kunt ook HTTP-aanvragen op basis van onbewerkte gegevens verzenden om de webservice te testen.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep en werkruimte wilt bewaren voor andere zelfstudies en voor verkenning, kunt u deze API-aanroep gebruiken om alleen de Container Instances-implementatie te verwijderen:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over alle [implementatieopties voor Azure Machine Learning](how-to-deploy-and-where.md).
+ Meer informatie over het [maken van clients voor de webservice](how-to-consume-web-service.md).
+  Doe asynchroon [voorspellingen op grote hoeveelheden gegevens](./tutorial-pipeline-batch-scoring-classification.md).
+ Bewaak uw Azure Machine Learning-modellen met [Application Insights](how-to-enable-app-insights.md).
+ Ga ook aan de slag met de zelfstudie [Automatisch algoritmen selecteren](tutorial-auto-train-models.md).