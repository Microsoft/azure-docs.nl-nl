---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 6a6b0d9740d19270f8daa3608bc125edd0fbec37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005080"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Formulieren analyseren voor sleutel-waardeparen en tabellen

U gaat nu uw pas getrainde model gebruiken voor het analyseren van een document en het extraheren van de sleutel-waardeparen en tabellen ervan. Roep de API **[Formulier analyseren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** aan door de volgende code uit te voeren in een nieuw Python-script. Voordat u het script uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<file path>` door het bestandspad van uw formulier (bijvoorbeeld C:\temp\file.pdf). Dit kan ook de URL zijn van een extern bestand. Voor deze quickstart kunt u de bestanden in de map **Testen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken (downloaden en extraheren van *sample_data.zip*).
1. Vervang `<model_id>` door de model-id in de vorige stap.
1. Vervang `<endpoint>` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `<file type>` door het bestandstype. Ondersteunde typen zijn: `application/pdf`, `image/jpeg`, `image/png`, `image/tiff`.
1. Vervang `<subscription key>` door uw abonnementssleutel.

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    
    # <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    


    ---



1. Sla de code op in een bestand met de extensie .py. Bijvoorbeeld *form-recognizer-analyze.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-analyze.py`.

Wanneer u de API **Formulier analyseren** aanroept, ontvangt u een `201 (Success)`-antwoord met een **Bewerking-Locatie**-header. De waarde van deze header is een id die u gebruikt om de resultaten van de analysebewerking bij te houden. Het bovenstaande script drukt de waarde van deze header af naar de console.

## <a name="get-the-analyze-results"></a>De analyseresultaten ophalen

Voeg onderaan uw Python-script de volgende code toe. Deze gebruikt de ID-waarde van de vorige aanroep in een nieuwe API-aanroep om de analyseresultaten op te halen. De bewerking **Formulier analyseren** is asynchroon. Dat betekent dat dit script de API regelmatig aanroept totdat de resultaten beschikbaar zijn. We raden een interval van minimaal één seconde aan.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
