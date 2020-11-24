---
title: 'Snelstart: een miniatuur maken - REST, Python'
titleSuffix: Azure Cognitive Services
description: In deze snelstart maakt u een miniatuur van een afbeelding met behulp van de Computer Vision-API en Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: ab9c20ebc8377fd388b597e10284397310519442
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95745755"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Quickstart: Een miniatuur genereren met de Computer Vision REST API en Python

In deze quickstart genereert u een miniatuur uit een afbeelding met behulp van de Computer Vision REST API. Met de methode [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20c) kunt u de gewenste hoogte en breedte opgeven. Computer Vision maakt gebruik van slim bijsnijden om op intelligente wijze het interessegebied te bepalen en coördinaten voor het bijsnijden te genereren op basis van dat gebied.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Maak een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* [Maak omgevingsvariabelen](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de sleutel- en eindpunt-URL, met respectievelijk de namen `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`.
- Een code-editor zoals [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Het voorbeeld maken en uitvoeren

Als u het voorbeeld wilt maken en uitvoeren, kopieert u de volgende code naar de code-editor. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Ga daarna als volgt te werk:

1. (Optioneel): vervang de waarde van `image_url` door de URL van uw eigen afbeelding.
1. Sla de code op als een bestand met de extensie `.py`. Bijvoorbeeld `get-thumbnail.py`.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd als binaire gegevens, die staan voor de afbeeldingsgegevens van de miniatuur. Deze afbeelding moet worden weergegeven in het voorbeeld. Als de aanvraag mislukt, wordt het antwoord weergegeven in het opdrachtpromptvenster en bevat het antwoord een foutcode.

## <a name="run-in-jupyter-optional"></a>Uitvoeren in Jupyter-(optioneel)

U kunt deze quickstart desgewenst stapsgewijs uitvoeren met behulp van een Jupyter Notebook op [MyBinder](https://mybinder.org). Selecteer de volgende knop om Binder te starten:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Volgende stappen

Een Python-toepassing verkennen die Computer Vision gebruikt om optische tekenherkenning (OCR) uit te voeren; slim bijgesneden miniaturen maken; en visuele kenmerken in een afbeelding detecteren, categoriseren, labelen en beschrijven.

> [!div class="nextstepaction"]
> [Zelfstudie voor de Computer Vision-API met Python](https://github.com/Microsoft/Cognitive-Vision-Python)

* Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20c).