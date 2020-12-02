---
title: 'Quickstart: Visitekaartjesgegevens extraheren met behulp van Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de Form Recognizer REST API met Python om gegevens te extraheren uit afbeeldingen van Engelstalige visitekaartjes.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 67a21dd86059f6cf1f017ce3eada285d2faab1e6
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012421"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Quickstart: Visitekaartjesgegevens extraheren met behulp van de Form Recognizer REST API met Python

In deze quickstart gebruikt u de Azure Form Recognizer REST API met Python om relevante informatie op visitekaartjes te extraheren en identificeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:
- [Python](https://www.python.org/downloads/) moet zijn geïnstalleerd (als u het voorbeeld lokaal wilt uitvoeren).
- Een afbeelding van een visitekaartje. U kunt voor deze quickstart een [voorbeeldafbeelding](../media/business-card-english.jpg) gebruiken.

> [!NOTE]
> In deze quickstart wordt een lokaal bestand gebruikt. Raadpleeg de [referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) als u een externe afbeelding van een visitekaartje wilt gebruiken die via URL wordt geopend.

## <a name="create-a-form-recognizer-resource"></a>Een Form Recognizer-resource maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Een visitekaartje analyseren

Als u wilt beginnen met het analyseren van een visitekaartje, roept u de **[Visitekaartje analyseren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** -API aan met het Python-script hieronder. Voordat u het script uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<endpoint>` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `<path to your business card>` door het lokale pad naar de afbeelding of PDF van uw visitekaartje.
1. Vervang `<subscription key>` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `<file type>` door 'image/jpeg', 'image/png', 'application/pdf' of 'image/tiff'.

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Sla de code op in een bestand met de extensie .py. Bijvoorbeeld *form-recognizer-businesscards.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-businesscards.py`.

U ontvangt een `202 (Success)`-antwoord met een **Operation-Location**-header, die het script naar de console afdrukt. Deze header bevat een resultaat-id die u kunt gebruiken om query's uit te voeren op de status van de langdurige bewerking en de resultaten op te halen. In de volgende voorbeeldwaarde is de tekenreeks na `operations/` de resultaat-id.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>De visitekaartjesresultaten ophalen

Nadat u de **Visitekaartje analyseren**-API hebt aangeroepen, roept u de **[Resultaat van Visitekaartje analyseren ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** -API aan om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voeg onderaan uw Python-script de volgende code toe. Dit maakt gebruik van de resultaat-id-waarde in een nieuwe API-aanroep. Met dit script wordt de API met regelmatige tussenpozen aangeroepen totdat de resultaten beschikbaar zijn. We raden een interval van minimaal één seconde aan.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Sla het script op.
1. Gebruik opnieuw de `python`-opdracht om de sample uit te voeren. Bijvoorbeeld `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Het antwoord bekijken
![Een visitekaartje van het Contoso-bedrijf](../media/business-card-english.jpg)

In dit voorbeeld wordt de JSON-uitvoer weergegeven die Form Recognizer retourneert. De uitvoer is ingekort zodat deze beter leesbaar is.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Met het script worden antwoorden naar de console afgedrukt totdat de **Visitekaartje analyseren**-bewerking is voltooid. Het knooppunt `"readResults"` bevat alle herkende tekst. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. Het knooppunt `"documentResults"` bevat de visitekaartjeswaarden die het model heeft gedetecteerd. Hier vindt u nuttige contactgegevens zoals bedrijfsnaam, voornaam, achternaam, telefoonnummer, enzovoort.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Form Recognizer REST API met Python gebruikt om de inhoud van een visitekaartje te extraheren. Raadpleeg hierna de referentiedocumentatie om de Form Recognizer API verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
