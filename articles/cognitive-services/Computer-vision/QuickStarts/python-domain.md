---
title: 'Quickstart: Domeinspecifieke inhoud - REST, Python'
titleSuffix: Azure Cognitive Services
description: In deze snelstart gebruikt u domeinmodellen om beroemdheden en oriëntatiepunten in een afbeelding te identificeren met behulp van de Computer Vision-API met Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 8cf49d6a088804f9b80de6d9debd9f0c7fba0771
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95745840"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Quickstart: Een externe domeinmodel gebruiken met de REST-API en Python in Computer Vision

In deze quickstart gebruikt u een domeinmodel om oriëntatiepunten, of eventueel beroemdheden, in een extern opgeslagen afbeelding te identificeren met behulp van de REST API van Computer Vision. Met de methode [Domeinspecifieke inhoud herkennen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f311) past u een domeinspecifiek model toe om inhoud in een afbeelding te herkennen.

U kunt deze snelstart stapsgewijs uitvoeren met behulp van een Jupyter Notebook op [MyBinder](https://mybinder.org). Selecteer de volgende knop om Binder te starten:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/downloads/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* [Maak omgevingsvariabelen](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de sleutel- en eindpunt-URL, met respectievelijk de namen `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-landmarks-sample"></a>Het voorbeeld met oriëntatiepunten maken en uitvoeren

U kunt het voorbeeld met oriëntatiepunten maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende code naar een teksteditor.
1. Vervang eventueel de waarde van `image_url` door de URL van een andere afbeelding waarin u oriëntatiepunten wilt herkennen.
1. Sla de code op als een bestand met de extensie `.py`. Bijvoorbeeld `get-landmarks.py`.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python get-landmarks.py`.

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

landmark_analyze_url = endpoint + "vision/v3.1/models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'landmarks'}
data = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>Het antwoord voor het voorbeeld met de oriëntatiepunten bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. De voorbeeldwebpagina parseert en geeft een geslaagd antwoord weer in het opdrachtpromptvenster dat vergelijkbaar is met het volgende voorbeeld:

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>Het voorbeeld met beroemdheden maken en uitvoeren

U kunt het voorbeeld met oriëntatiepunten maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende code naar een teksteditor.
1. Breng waar nodig de volgende wijzigingen in code aan:
    1. Vervang de waarde van `subscription_key` door uw abonnementssleutel.
    1. Vervang de waarde van `vision_base_url` door de eindpunt-URL voor de Computer Vision-bron in de Azure-regio waar u uw abonnementssleutels hebt verkregen (indien nodig).
    1. Vervang eventueel de waarde van `image_url` door de URL van een andere afbeelding waarin u beroemdheden wilt herkennen.
1. Sla de code op als een bestand met de extensie `.py`. Bijvoorbeeld `get-celebrities.py`.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python get-celebrities.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'celebrities'}
data = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>Het antwoord voor het voorbeeld met de beroemdheden bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. De voorbeeldwebpagina parseert en geeft een geslaagd antwoord weer in het opdrachtpromptvenster dat vergelijkbaar is met het volgende voorbeeld:


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de bestanden van beide voorbeelden wanneer u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

Een Python-toepassing verkennen die Computer Vision gebruikt om optische tekenherkenning (OCR) uit te voeren; slim bijgesneden miniaturen maken; en visuele kenmerken in een afbeelding detecteren, categoriseren, labelen en beschrijven.

> [!div class="nextstepaction"]
> [Zelfstudie voor de Computer Vision-API met Python](https://github.com/Microsoft/Cognitive-Vision-Python)

* Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console).
