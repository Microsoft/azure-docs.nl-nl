---
title: Quickstart voor Bing Custom Search-clientbibliotheek voor Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 3019881c42e0f7b64cc766b8b9e575eb60612432
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948342"
---
Aan de slag met de clientbibliotheek van Bing Custom Search voor Python. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. Met de API van Bing Custom Search kunt u op maat gemaakte zoekervaringen zonder advertenties maken voor onderwerpen die u interesseren. De broncode voor dit voorbeeld vindt u op [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Gebruik de Bing Custom Search-clientbibliotheek voor Python om het volgende te doen:
* Zoekresultaten zoeken op internet vanuit uw exemplaar van Bing Custom Search.

[Referentiedocumentatie](/python/api/azure-cognitiveservices-search-customsearch/) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Quickstart: Uw eerste Bing Custom Search-exemplaar maken](../../quick-start.md) voor meer informatie.
- Python [2.x of 3.x](https://www.python.org/) 
- De [Bing Custom Search-SDK voor Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>De Python-clientbibliotheek installeren

Installeer de Bing Custom Search-clientbibliotheek met de volgende opdracht.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

Maak een nieuw Python-bestand in uw favoriete editor of IDE en voeg de volgende importinstructies toe.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Een zoekclient aanmaken en een aanvraag verzenden

1. Maak een variabele voor uw abonnementssleutel en eindpunt.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Maak een exemplaar van `CustomSearchClient` met behulp van een `CognitiveServicesCredentials`-object en de abonnementssleutel. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Verzend een zoekaanvraag met `client.custom_instance.search()`. Voeg uw zoekterm toe aan de parameter `query` en stel `custom_config` in op uw Custom-configuratie-ID om uw zoekexemplaar te gebruiken. U kunt uw ID ophalen via de [Bing Custom Search-portal](https://www.customsearch.ai/) door te klikken op het tabblad **Productie**.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>De zoekresultaten weergeven

Haal, als er zoekresultaten voor webpagina's zijn gevonden, de eerste op en toon de naam, de URL en het totale aantal gevonden webpagina's.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](../../tutorials/custom-search-web-page.md)
