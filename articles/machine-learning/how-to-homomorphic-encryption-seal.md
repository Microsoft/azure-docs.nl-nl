---
title: Een versleutelde service voor het afnemen van interferentie implementeren (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van micro soft afdichting voor het implementeren van een versleutelde Voorspellings service voor de classificatie van afbeeldingen
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, responsible-ml
ms.openlocfilehash: a2c8b765f109408b8cb08f78a4b3ad2283f35eb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223581"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Een gecodeerde webservice voor het afwijzen van een versleutelings service implementeren (preview)

Meer informatie over het implementeren van een installatie kopie-classificatie model als een versleutelde webservice voor het afwijzen van een provider in [Azure container instances](../container-instances/index.yml) (ACI). De webservice is een docker-container installatie kopie die het model en de Score logica bevat.

In deze hand leiding gebruikt u Azure Machine Learning-service voor het volgende:

> [!div class="checklist"]
> * Uw omgevingen configureren
> * Versleutelde webservice implementeren
> * Test gegevens voorbereiden
> * Versleutelde voor spellingen maken
> * Resources opschonen

ACI is een geweldige oplossing voor het testen en het leren van de implementatie werk stroom voor modellen. Voor schaalbare productie-implementaties is het misschien beter om Azure Kubernetes Service te gebruiken. Zie [Modellen implementeren met de Azure Machine Learning-service](./how-to-deploy-and-where.md) voor meer informatie.

De versleutelings methode die in dit voor beeld wordt gebruikt, is [Homomorphic-versleuteling](https://github.com/Microsoft/SEAL#homomorphic-encryption). Met Homomorphic versleuteling kunnen berekeningen worden uitgevoerd op versleutelde gegevens zonder dat daarvoor toegang nodig is tot een geheime sleutel (ontsleuteling). De resultaten van de berekeningen zijn versleuteld en kunnen alleen worden onthuld door de eigenaar van de geheime sleutel. 

## <a name="prerequisites"></a>Vereisten

In deze hand leiding wordt ervan uitgegaan dat u een installatie kopie classificatie model hebt geregistreerd in Azure Machine Learning. Als dat niet het geval is, registreert u het model met behulp van een [voortraind model](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) of maakt u zelf een [installatie kopie classificatie model met Azure machine learning zelf studie](tutorial-train-models-with-aml.md).

## <a name="configure-local-environment"></a>Lokale omgeving configureren

In een Jupyter-notebook

1. Importeer de Python-pakketten die nodig zijn voor dit voor beeld.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Installeer de Homomorphic-versleutelings bibliotheek voor veilige interferentie.

    > [!NOTE]
    > Het `encrypted-inference` pakket is momenteel beschikbaar als preview-versie.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) is een bibliotheek die bindingen bevat voor versleutelde interferentie op basis van [micro soft-zegel](https://github.com/Microsoft/SEAL).

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>De omgeving voor het afwijzen van interferentie configureren

Maak een omgeving om een pakket te maken en toe te voegen `encrypted-inference` als een Conda-afhankelijkheid.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Versleutelde webservice implementeren

Implementeer het model als een webservice die wordt gehost in ACI.

Geef de volgende zaken op om de juiste omgeving te maken voor ACI:

* Een scoring-script dat aangeeft hoe het model moet worden gebruikt
* Een configuratiebestand om de ACI mee te bouwen
* Een getraind model

### <a name="create-scoring-script"></a>Scoring-script maken

Het Score script maken `score.py` dat wordt gebruikt door de webservice voor demijnen.

Er zijn twee functies die u verplicht in het scoring-script moet opnemen:

* De functie `init()`, die het model doorgaans in een algemeen object laadt. Deze functie wordt slechts één keer uitgevoerd wanneer de Docker-container wordt gestart.
* De functie `run(input_data)` gebruikt het model om een waarde te voorspellen op basis van de invoergegevens. De in- en uitvoer van de uitvoerbewerking maken doorgaans gebruik van JSON voor serialisatie en deserialisatie, maar andere indelingen worden ook ondersteund. Met de functie worden open bare sleutels op basis van Homomorphic-versleuteling opgehaald die door de service aanroepener worden geüpload.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Een configuratiebestand maken

Maak een configuratiebestand voor de implementatie en geef het aantal CPU's en het aantal gigabytes aan RAM-geheugen op dat nodig is voor uw ACI-container. Het verschilt per model, maar voor de meeste modellen is de standaardhoeveelheid van 1 kerngeheugen en 1 gigabyte aan RAM-geheugen wel voldoende. Als u er later meer nodig hebt, moet u de installatiekopie opnieuw maken en de service opnieuw implementeren.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Implementeren naar Azure Container Instances

Geschatte duur: **ongeveer 2 tot 5 minuten**

Configureer en implementeer de installatiekopie. De volgende code doorloopt de volgende stappen:

1. Maak een omgevings object met afhankelijkheden die nodig zijn voor het model met behulp van het omgevings bestand ( `myenv.yml` )
1. Maak de deductieconfiguratie die nodig is voor het implementeren van het model als een webservice met behulp van:
   * Het scoring-bestand (`score.py`)
   * Het omgevings object dat u in de vorige stap hebt gemaakt
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

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Haal het HTTP-eindpunt van de scoring-webservice op, die REST-clientaanroepen accepteert. Dit eindpunt kan worden gedeeld met iedereen die de webservice wil testen of wil integreren in een toepassing.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Test gegevens voorbereiden

1. Down load de test gegevens. In dit geval wordt het opgeslagen in een map met de naam *gegevens*.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Laad de test gegevens vanuit de *Data* Directory.

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Versleutelde voor spellingen maken

Gebruik de gegevensset testen met het model voor het verkrijgen van voor spellingen.

Versleutelde voor spellingen maken:

1. Maak een nieuwe `EILinearRegressionClient` , een Homomorphic-versleutelings-gebaseerde client en open bare sleutels.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Homomorphic-code ring gegenereerde open bare sleutels uploaden naar de werk ruimte standaard BLOB Store. Hiermee kunt u de sleutels delen met de server voor afleiden.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. De test gegevens versleutelen

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. Gebruik de API van de SDK `run` om de service aan te roepen en de test-gegevensset aan het model toe te voegen voor het verkrijgen van voor spellingen. We moeten de connection string verzenden naar de Blob-opslag waar de open bare sleutels zijn geüpload.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. De-client gebruiken om de resultaten te ontsleutelen.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Resources opschonen

De webservice verwijderen die in dit voor beeld is gemaakt:

```python
service.delete()
```

Als u de Azure-resources die u hebt gemaakt, niet meer wilt gebruiken, verwijdert u deze. Zo voor komt u dat er kosten in rekening worden gebracht voor ongebruikte resources die nog worden uitgevoerd. Raadpleeg deze hand leiding voor meer informatie over het [opschonen van resources](how-to-manage-workspace.md#clean-up-resources) .