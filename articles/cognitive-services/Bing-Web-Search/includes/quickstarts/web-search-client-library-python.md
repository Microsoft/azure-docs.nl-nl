---
title: Quickstart voor Bing Web Search-clientbibliotheek voor Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: db303d1e7bd5a9cb7b8441f0a5da634a13113066
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947300"
---
Met de Bing Web Search-clientbibliotheek kunt u Bing Web Search eenvoudig integreren in uw Python-toepassing. In deze snelstartgids leert u hoe u een aanvraag verzendt, een JSON-antwoord ontvangt en de resultaten filtert en parseert.

Wilt u de code nu zien? Voorbeelden voor de [Bing Search-clientbibliotheken voor Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/search) zijn beschikbaar op GitHub.


## <a name="prerequisites"></a>Vereisten
De Bing Web Search SDK is compatibel met Python 2.7, 3.3, 3.4, 3.5 en 3.6. We raden u aan om een virtuele omgeving te gebruiken voor deze snelstartgids.

* Python 2.7, 3.3, 3.4, 3.5 of 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) voor Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) voor Python 3.x

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

## <a name="create-and-configure-your-virtual-environment"></a>Uw virtuele omgeving maken en configureren

De instructies voor het opzetten en configureren van een virtuele omgeving zijn verschillend voor Python 2.x en Python 3.x. Voer de onderstaande stappen uit om uw virtuele omgeving te maken en te initialiseren.

### <a name="python-2x"></a>Python 2.x

Maak een virtuele omgeving met `virtualenv` voor Python 2.7:

```console
virtualenv mytestenv
```

Uw omgeving activeren:

```console
cd mytestenv
source bin/activate
```

Installeer Bing Web Search SDK-afhankelijkheden:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Maak een virtuele omgeving met `venv` voor Python 3.x:

```console
python -m venv mytestenv
```

Uw omgeving activeren:

```console
mytestenv\Scripts\activate.bat
```

Installeer Bing Web Search SDK-afhankelijkheden:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Een client maken en uw eerste resultaten weergeven

Nu u de virtuele omgeving hebt ingesteld en de afhankelijkheden hebt geïnstalleerd, kunnen we een client maken. De client verwerkt aanvragen voor en antwoorden afkomstig van de Bing Webzoekopdrachten-API.

Als het antwoord webpagina's, afbeeldingen, nieuws of video's bevat, wordt het eerste resultaat voor elk item weergegeven.

1. Maak een nieuw Python-project met uw favoriete IDE of editor.

1. Kopieer de volgende voorbeeldcode naar uw project. `endpoint` kan het volgende globale eindpunt zijn of het eindpunt [aangepast subdomein](~/articles/cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw resource.:  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchClient
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client and replace with your endpoint.
    client = WebSearchClient(endpoint="YOUR_ENDPOINT", credentials=CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. Vervang `SUBSCRIPTION_KEY` door een geldige abonnementssleutel.

1. Vervang `YOUR_ENDPOINT` door de eindpunt-URL in de portal en verwijder het gedeelte "bing/v7.0" uit het eindpunt.

1. Voer het programma uit. Bijvoorbeeld: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Functies definiëren en resultaten filteren

Nu u uw eerste aanroep hebt gedaan voor de Bing Web Search-API, gaan we eens een paar functies bekijken. De volgende secties gaan over de SDK-functionaliteit voor het verfijnen van query's en het filteren van resultaten. Deze functies kunnen worden toegevoegd aan uw Python-programma dat in de vorige sectie is gemaakt.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Het aantal resultaten beperken dat door Bing wordt geretourneerd

In dit voorbeeld worden de parameters `count` en `offset` gebruikt voor het beperken van het aantal resultaten dat wordt geretourneerd met de [`search`-methode](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations) van de SDK. De `name` en `url` voor het eerste resultaat worden weergegeven.

1. Voeg deze code toe aan uw Python-project:

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Voer het programma uit.

### <a name="filter-for-news-and-freshness"></a>Filteren op nieuws en actuele items

In dit voorbeeld worden de parameters `response_filter` en `freshness` gebruikt om de zoekresultaten te filteren met de [`search`-methode](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations) van de SDK. De geretourneerde zoekresultaten zijn beperkt tot nieuwsartikelen en pagina's die Bing heeft gedetecteerd gedurende de afgelopen 24 uur. De `name` en `url` voor het eerste resultaat worden weergegeven.

1. Voeg deze code toe aan uw Python-project:

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. Voer het programma uit.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Gebruik de parameters voor veilig zoeken, aantal antwoorden en het filter voor het promoten van zoekresultaten

In dit voorbeeld worden de parameters `answer_count`, `promote` en `safe_search` gebruikt om de zoekresultaten te filteren met de [`search`-methode](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations) van de SDK. De `name` en `url` voor het eerste resultaat worden weergegeven.

1. Voeg deze code toe aan uw Python-project:

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
            if web_data.web_pages.value:

                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't see any Web data..")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Voer het programma uit.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met dit project, moet u uw abonnementssleutel verwijderen uit de code van het programma en uw virtuele omgeving deactiveren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Voorbeelden voor Cognitive Services Python SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Zie ook

* [Naslaginformatie over Azure Python SDK](/python/api/azure-cognitiveservices-search-websearch/)
