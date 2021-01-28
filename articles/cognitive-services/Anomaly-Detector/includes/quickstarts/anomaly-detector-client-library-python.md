---
title: Quickstart voor Anomaly Detector-clientbibliotheek voor Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: f6206ad2f88983396fa7d0be323daad327e4d235
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948430"
---
Ga aan de slag met de Anomaly Detector-clientbibliotheek voor Python. Voer de volgende stappen uit om het pakket te installeren en de algoritmen te gaan gebruiken die door de service worden geleverd. Met de Anomaly Detector-service kunt u afwijkingen in uw tijdreeksgegevens vinden door hierop automatisch de best passende modellen uit te voeren, ongeacht de branche, het scenario of het gegevensvolume.

Gebruik de Anomaly Detector-clientbibliotheek voor Python om:

* Anomalieën in uw tijdreeksgegevensset als een batchaanvraag te detecteren
* De anomaliestatus van het laatste gegevenspunt in uw tijdreeks te detecteren
* Trendwijzigingspunten in uw gegevensset te detecteren.

[Referentiedocumentatie voor bibliotheek](https://go.microsoft.com/fwlink/?linkid=2090370) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Pakket (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [De voorbeeldcode zoeken op GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>Vereisten

* [Python 3.x](https://www.python.org/)
* De [bibliotheek voor Pandas-gegevensanalyse](https://pandas.pydata.org/)
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">maakt u een Anomaly Detector-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.


## <a name="setting-up"></a>Instellen

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

 Maak een nieuw Python-bestand en importeer de volgende bibliotheken.

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

Maak variabelen voor uw sleutel als een omgevingsvariabele, het pad naar een tijdreeksgegevensbestand en de Azure-locatie van uw abonnement. Bijvoorbeeld `westus2`.

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Na de installatie van Python kunt u de clientbibliotheek installeren met:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Objectmodel

De Anomaly Detector-client is een [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py)-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De client kan anomaliedetectie uitvoeren op een hele gegevensset met behulp van [detect_entire_series](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26), of op het laatste gegevenspunt met behulp van [detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87). De functie [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) detecteert punten die wijzigingen in een trend markeren.

Tijdreeksgegevens worden verzonden als een reeks [TimeSeriesPoints](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370)-objecten. Het `DetectRequest`-object bevat eigenschappen die bijvoorbeeld de `TimeGranularity` van gegevens beschrijven en parameters voor de anomaliedetectie.

Het antwoord van Anomaly Detector is een [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse)-, [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse)- of [ChangePointDetectResponse](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107)-object afhankelijk van de gebruikte methode.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Anomaly Detector-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Een tijdreeksgegevensset laden vanuit een bestand](#load-time-series-data-from-a-file)
* [Anomalieën in de volledige gegevensset detecteren](#detect-anomalies-in-the-entire-data-set)
* [De anomaliestatus van het laatste gegevenspunt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)
* [De wijzigingspunten in de gegevensset detecteren](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>De client verifiëren

Voeg uw Azure-locatievariabele toe aan het eindpunt en verifieer de client met uw sleutel.

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>Tijdreeksgegevens uit een bestand laden

Download de voorbeeldgegevens voor deze quickstart van [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Klik in uw browser met de rechtermuisknop op **Onbewerkt**.
2. Klik op **Koppeling opslaan als**.
3. Sla het bestand op in de map van de toepassing als CSV-bestand.

Deze tijdreeksgegevens worden opgemaakt als CSV-bestand en worden verzonden naar de Anomaly Detector-API.

Laad uw gegevensbestand met de `read_csv()`-methode van de Pandas-bibliotheek en maak een lege lijstvariabele om uw gegevensreeksen op te slaan. Herhaal het bestand en voeg de gegevens toe als `TimeSeriesPoint`-object. Dit object bevat de tijdstempel en de numerieke waarde van de rijen van uw CSV-gegevensbestand.

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

Maak een `DetectRequest`object met uw tijdreeks en de `TimeGranularity` (of periodiciteit) van de gegevenspunten. Bijvoorbeeld `TimeGranularity.daily`.

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

## <a name="detect-anomalies-in-the-entire-data-set"></a>Anomalieën in de volledige gegevensset detecteren

Roep de API aan om anomalieën te detecteren via de volledige tijdreeksgegevens met behulp van de `detect_entire_series`-methode van de client. Sla het geretourneerde [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse)-object op. Herhaal de `is_anomaly`-lijst van de reactie en druk de index van alle `true`-waarden af. Deze waarden komen overeen met de index van afwijkende gegevenspunten, als die zijn gevonden.

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

Roep de Anomaly Detector-API aan om te bepalen of uw laatste gegevenspunt een anomalie is met behulp van de `detect_last_point`-methode van de client en sla het geretourneerde `LastDetectResponse`-object op. De `is_anomaly`-waarde van het antwoord is een booleaanse waarde die de afwijkingsstatus van het punt opgeeft.  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>Wijzigingspunten in de gegevensset detecteren

Roep de API aan om wijzigingspunten in de tijdreeksgegevens te detecteren met behulp van de methode `detect_change_point` van de client. Sla het geretourneerde `ChangePointDetectResponse`-object op. Herhaal de `is_change_point`-lijst van de reactie en druk de index van alle `true`-waarden af. Deze waarden komen overeen met de indexen van trendwijzigingspunten, als die zijn gevonden.

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `python` en uw bestandsnaam.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
