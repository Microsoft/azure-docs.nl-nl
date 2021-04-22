---
title: Anomaly Detector quickstart voor multivariate Clientbibliotheek van Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: b0eba6d0be1d6a65b911f05dabf3cdbecc9c666d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879988"
---
Ga aan de slag met Anomaly Detector multivariate clientbibliotheek voor Python. Voer de volgende stappen uit om het pakket te installeren en de algoritmen te gaan gebruiken die door de service worden geleverd. Met de nieuwe API's voor anomaliedetectie met meerdere afwijkingen kunnen ontwikkelaars eenvoudig geavanceerde AI integreren voor het detecteren van afwijkingen uit groepen met metrische gegevens, zonder dat machine learning kennis of gelabelde gegevens nodig zijn. Afhankelijkheden en onderlinge correlaties tussen verschillende signalen worden automatisch geteld als belangrijke factoren. Dit helpt u om uw complexe systemen proactief te beschermen tegen storingen.

Gebruik de Anomaly Detector multivariate clientbibliotheek voor Python voor het volgende:

* Anomalieën op systeemniveau detecteren uit een groep tijdreeksen.
* Wanneer een afzonderlijke tijdreeks u niet veel vertelt en u alle signalen moet bekijken om een probleem te detecteren.
* Predicatief onderhoud van dure fysieke activa met tientallen tot honderden verschillende soorten sensoren die verschillende aspecten van de systeemtoestand meten.

[Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Pakket (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/)  |  [Voorbeeldcode](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)  |  [Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb)

## <a name="prerequisites"></a>Vereisten

* [Python 3.x](https://www.python.org/)
* De [bibliotheek voor Pandas-gegevensanalyse](https://pandas.pydata.org/)
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">maakt u een Anomaly Detector-resource </a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.


## <a name="setting-up"></a>Instellen

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

 Maak een nieuw Python-bestand en importeer de volgende bibliotheken.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Maak variabelen voor uw sleutel als een omgevingsvariabele, het pad naar een tijdreeksgegevensbestand en de Azure-locatie van uw abonnement. Bijvoorbeeld `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Na de installatie van Python kunt u de clientbibliotheek installeren met:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Anomaly Detector-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Het model trainen](#train-the-model)
* [Afwijkingen detecteren](#detect-anomalies)
* [Model exporteren](#export-model)
* [Model verwijderen](#delete-model)

## <a name="authenticate-the-client"></a>De client verifiëren

Als u een nieuwe client wilt Anomaly Detector, moet u de Anomaly Detector en het bijbehorende eindpunt doorgeven. We maken ook een gegevensbron.  

Als u de Anomaly Detector api's wilt gebruiken, moeten we ons eigen model trainen voordat u detectie gebruikt. Gegevens die worden gebruikt voor training zijn een batch tijdreeksen. Elke tijdreeks moet een CSV-indeling hebben met twee kolommen, tijdstempel en waarde. Alle tijdreeksen moeten worden ingepakt in één zip-bestand en worden geüpload naar [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Standaard wordt de bestandsnaam gebruikt om de variabele voor de tijdreeks weer te geven. U kunt ook een extra meta.jsin het zip-bestand als u wilt dat de naam van de variabele verschilt van de naam van het ZIP-bestand. Zodra we de [BLOB SAS-URL (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)hebben gegenereerd, kunnen we de URL naar het ZIP-bestand gebruiken voor training.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>Het model trainen

Eerst trainen we het model, controleren we de status van het model tijdens het trainen om te bepalen wanneer de training is voltooid en halen we vervolgens de meest recente model-id op die we nodig hebben wanneer we naar de detectiefase gaan.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Afwijkingen detecteren

Gebruik de `detect_anomaly` en om te bepalen of er afwijkingen in uw `get_dectection_result` gegevensbron zijn. U moet de model-id doorgeven voor het model dat u zojuist hebt getraind.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Model exporteren

Als u een modelgebruik wilt exporteren en `export_model` de model-id wilt doorgeven van het model dat u wilt exporteren:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Model verwijderen

Als u een modelgebruik wilt `delete_multivariate_model` verwijderen en de model-id wilt doorgeven van het model dat u wilt verwijderen:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voordat u de toepassing gaat uitvoeren, moeten we code toevoegen om onze zojuist gemaakte functies aan te roepen.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Voordat u het project gaat uitvoeren, [](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) kan het handig zijn om uw project te controleren aan de hand van de volledige voorbeeldcode waar deze quickstart van is afgeleid.

We hebben ook [een uitgebreide Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) om u op weg te helpen.

Voer de toepassing uit met de opdracht `python` en uw bestandsnaam.


[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
