---
title: 'Snelstartgids: Een zoekopdracht uitvoeren met Python - Bing Webzoekopdrachten-API'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om aanvragen naar de REST API van Bing Web Search te verzenden via Python en een JSON-antwoord te ontvangen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 5a9eff1ef012e66f661ee99852a8d82f117a45c4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350514"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Snelstartgids: Python gebruiken voor het aanroepen van de Bing Webzoekopdrachten-API  

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om de Bing Web Search API voor de eerste keer aan te roepen. Deze Python-app verzendt een zoekaanvraag naar de API en geeft het JSON-antwoord weer. Hoewel deze toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Dit voorbeeld wordt uitgevoerd als een Jupyter-notitieblok in [MyBinder](https://mybinder.org). Als u deze wilt uitvoeren, selecteert u de badge Binder starten:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Vereisten

* [Python 2.x of 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Variabelen definiëren

1. Vervang de waarde `subscription_key` door een geldige abonnementssleutel uit uw Azure-account.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Declareer het eindpunt voor de Bing Webzoekopdrachten-API. U kunt het globale eindpunt in de volgende code gebruiken of het eindpunt voor een [aangepast subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource.

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. U kunt de zoekquery eventueel aanpassen door de waarde voor `search_term` te vervangen.

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>Een aanvraag maken

Deze code maakt gebruik van de `requests`-bibliotheek om de Bing Web Search-API aan te roepen en de resultaten als een JSON-object te retourneren. De API-sleutel wordt doorgegeven in de `headers`-woordenlijst, en de zoekterm en queryparameters worden doorgegeven in de `params`-woordenlijst. 

Raadpleeg [Bing Web Search-API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) voor een volledige lijst met opties en parameters.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Het antwoord opmaken en weergeven

Het object `search_results` bevat de zoekresultaten en metagegevens als gerelateerde query's en pagina's. Deze code maakt gebruikt van de `IPython.display`-bibliotheek om het antwoord in uw browser op te maken en weer te geven.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Voorbeeldcode in GitHub

Als u deze code lokaal wilt uitvoeren, raadpleegt u het volledige [voorbeeld op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie voor de app met één pagina voor de Bing Web Search API](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]