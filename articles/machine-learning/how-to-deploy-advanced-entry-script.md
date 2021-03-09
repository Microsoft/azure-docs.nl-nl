---
title: Invoer script voor geavanceerde scenario's
titleSuffix: Azure Machine Learning entry script authoring
description: Meer informatie over het schrijven van Azure Machine Learning invoer scripts voor vóór en na de verwerking tijdens de implementatie.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: f88b6bd79e1004c108ef868f910a4e5a974cc08a
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508534"
---
# <a name="advanced-entry-script-authoring"></a>Geavanceerde scriptinvoer ontwerpen

In dit artikel wordt beschreven hoe u invoer scripts voor gespecialiseerde use cases schrijft.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een getraind machine learning model hebt dat u wilt implementeren met Azure Machine Learning. Raadpleeg [deze zelf studie](how-to-deploy-and-where.md)voor meer informatie over het implementeren van modellen.

## <a name="automatically-generate-a-swagger-schema"></a>Automatisch een Swagger-schema genereren

Als u automatisch een schema voor uw webservice wilt genereren, geeft u een voor beeld van de invoer en/of uitvoer in de constructor voor een van de gedefinieerde type-objecten. Het type en het voor beeld worden gebruikt voor het automatisch maken van het schema. Azure Machine Learning maakt vervolgens tijdens de implementatie een [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger)-specificatie voor de webservice. 

> [!WARNING]
> U moet geen gevoelige of persoonlijke gegevens gebruiken voor voorbeeld invoer of-uitvoer. De Swagger-pagina voor de door de AML-hosten beproefde defactorion maakt de voorbeeld gegevens beschikbaar. 

Deze typen worden momenteel ondersteund:

* `pandas`
* `numpy`
* `pyspark`
* Standard python-object

Als u schema generatie wilt gebruiken, neemt u het open-source `inference-schema` pakket versie 1.1.0 of hoger op in het bestand met afhankelijkheden. Zie voor meer informatie over dit pakket [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Voor het genereren van Swagger voor geautomatiseerd gebruik van webservices moet de functie Score script run () de API-vorm hebben van:
* Een eerste para meter van het type ' StandardPythonParameterType ', met de naam **invoer** en genest.
* Een optionele tweede para meter van het type ' StandardPythonParameterType ' met de naam **GlobalParameters**.
* Retour neren van een woorden lijst van het type ' StandardPythonParameterType ' met de naam **resultaten** en genest.

Definieer de voorbeeld indelingen voor invoer en uitvoer in `input_sample` de `output_sample` variabelen en, die de aanvraag-en antwoord indelingen voor de webservice vertegenwoordigen. Gebruik deze voor beelden in de functie voor invoer en uitvoer op de `run()` functie. Het volgende voor beeld van scikit maakt gebruik van schema generatie.



## <a name="power-bi-compatible-endpoint"></a>Power BI compatibel eind punt 

In het volgende voor beeld ziet u hoe u API-shape definieert volgens de bovenstaande instructies. Deze methode wordt ondersteund voor het gebruik van de geïmplementeerde webservice van Power BI. (Meer[informatie over het gebruik van de webservice van Power bi](/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                        'input2': pandas_sample_input, 
                                        'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) # this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])
outputs = StandardPythonParameterType({'Results':sample_output}) # 'Results' is case sensitive

@input_schema('Inputs', sample_input) 
# 'Inputs' is case sensitive

@input_schema('GlobalParameters', sample_global_parameters) 
# this is optional, 'GlobalParameters' is case sensitive

@output_schema(outputs)

def run(Inputs, GlobalParameters): 
    # the parameters here have to match those in decorator, both 'Inputs' and 
    # 'GlobalParameters' here are case sensitive
    try:
        data = Inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Binaire (d.w.z. afbeeldings) gegevens

Als uw model binaire gegevens accepteert, zoals een afbeelding, moet u het bestand wijzigen dat `score.py` wordt gebruikt voor uw implementatie om onbewerkte HTTP-aanvragen te accepteren. Als u onbewerkte gegevens wilt accepteren, gebruikt u de `AMLRequest` klasse in uw invoer script en voegt `@rawhttp` u de decorator toe aan de `run()` functie.

Hier volgt een voor beeld van een `score.py` waarmee binaire gegevens worden geaccepteerd:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse
from PIL import Image
import json


def init():
    print("This is init()")
    

@rawhttp
def run(request):
    print("This is run()")
    
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        file_bytes = request.files["image"]
        image = Image.open(file_bytes).convert('RGB')
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the size of the image as the response..
        return AMLResponse(json.dumps(image.size), 200)
    else:
        return AMLResponse("bad request", 500)
```


> [!IMPORTANT]
> De `AMLRequest` klasse bevindt zich in de `azureml.contrib` naam ruimte. Entiteiten in deze naam ruimte worden regel matig gewijzigd, terwijl we werken om de service te verbeteren. Alles in deze naam ruimte moet worden beschouwd als een preview-versie die niet volledig wordt ondersteund door micro soft.
>
> Als u dit in uw lokale ontwikkel omgeving moet testen, kunt u de onderdelen installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest`Met de klasse hebt u alleen toegang tot de onbewerkte geposte gegevens in de score.py, maar er is geen onderdeel aan de client zijde. Vanaf een-client post u gegevens als normaal. Met de volgende python-code wordt bijvoorbeeld een afbeeldings bestand gelezen en worden de gegevens Gepost:

```python
import requests

uri = service.scoring_uri
image_path = 'test.jpg'
files = {'image': open(image_path, 'rb').read()}
response = requests.post(url, files=files)

print(response.json)
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>CORS (Cross-Origin Resource Sharing, cross-origin-resource delen)

Cross-Origin-resource delen is een manier om te voor komen dat resources op een webpagina worden aangevraagd vanuit een ander domein. CORS werkt via HTTP-headers die worden verzonden met de aanvraag van de client en worden geretourneerd met de service reactie. Zie [Cross-Origin-resource delen](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in Wikipedia voor meer informatie over CORS en geldige headers.

Als u uw model implementatie wilt configureren voor de ondersteuning van CORS, gebruikt u de- `AMLResponse` klasse in uw invoer script. Met deze klasse kunt u de headers op het antwoord object instellen.

In het volgende voor beeld wordt de `Access-Control-Allow-Origin` koptekst ingesteld voor het antwoord van het entry-script:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> De `AMLResponse` klasse bevindt zich in de `azureml.contrib` naam ruimte. Entiteiten in deze naam ruimte worden regel matig gewijzigd, terwijl we werken om de service te verbeteren. Alles in deze naam ruimte moet worden beschouwd als een preview-versie die niet volledig wordt ondersteund door micro soft.
>
> Als u dit in uw lokale ontwikkel omgeving moet testen, kunt u de onderdelen installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning worden alleen POST-en GET-aanvragen doorgestuurd naar de containers die de Score service uitvoeren. Dit kan fouten veroorzaken door browsers die gebruikmaken van OPTIONS-aanvragen voor CORS-aanvragen van voor bereidingen.
> 


## <a name="load-registered-models"></a>Geregistreerde modellen laden

Er zijn twee manieren om modellen te vinden in het involgings script:
* `AZUREML_MODEL_DIR`: Een omgevings variabele die het pad naar de model locatie bevat.
* `Model.get_model_path`: Een API die het pad naar het model bestand retourneert met de geregistreerde model naam.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR is een omgevings variabele die tijdens de implementatie van de service wordt gemaakt. U kunt deze omgevings variabele gebruiken om de locatie van de geïmplementeerde model (en) te vinden.

In de volgende tabel wordt de waarde van AZUREML_MODEL_DIR beschreven, afhankelijk van het aantal geïmplementeerde modellen:

| Implementatie | Waarde van omgevings variabele |
| ----- | ----- |
| Eén model | Het pad naar de map die het model bevat. |
| Meerdere modellen | Het pad naar de map met alle modellen. Modellen bevinden zich op naam en versie in deze map ( `$MODEL_NAME/$VERSION` ) |

Tijdens model registratie en-implementatie worden modellen in het AZUREML_MODEL_DIR pad geplaatst en hun oorspronkelijke bestands namen blijven behouden.

Als u het pad naar een model bestand in het invoer script wilt ophalen, combineert u de omgevings variabele met het pad naar het bestand waarnaar u op zoek bent.

**Voor beeld van één model**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Voor beeld van meerdere modellen**

In dit scenario worden twee modellen geregistreerd bij de werk ruimte:

* `my_first_model`: Bevat één bestand ( `my_first_model.pkl` ) en er is slechts één versie ( `1` ).
* `my_second_model`: Bevat één bestand ( `my_second_model.pkl` ) en er zijn twee versies, `1` en `2` .

Wanneer de service is geïmplementeerd, worden beide modellen in de implementatie bewerking weer gegeven:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

In de docker-installatie kopie die als host fungeert voor de service, `AZUREML_MODEL_DIR` bevat de omgevings variabele de map waarin de modellen zich bevinden.
In deze map bevindt elk model zich in een mappad van `MODEL_NAME/VERSION` . Waarbij de `MODEL_NAME` naam is van het geregistreerde model en `VERSION` de versie van het model is. De bestanden waaruit het geregistreerde model bestaan, worden opgeslagen in deze mappen.

In dit voor beeld zijn de paden `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` en `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Wanneer u een model registreert, geeft u een model naam op die wordt gebruikt voor het beheren van het model in het REGI ster. U gebruikt deze naam met de methode [model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) om het pad van het model bestand of de bestanden op het lokale bestands systeem op te halen. Als u een map of een verzameling bestanden registreert, retourneert deze API het pad van de map die de bestanden bevat.

Wanneer u een model registreert, geeft u het een naam. De naam komt overeen met de locatie waar het model lokaal of tijdens de service-implementatie wordt geplaatst.

## <a name="framework-specific-examples"></a>Framework-specifieke voor beelden

Hieronder vindt u meer voor beelden van script voorbeelden voor specifieke machine learning use cases:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

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
