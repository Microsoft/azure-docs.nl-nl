---
title: 'Quickstart: Aanroepen van uw Bing Custom Search-eindpunt met Node.js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om te beginnen met het opvragen van zoekresultaten van uw Bing Custom Search-instantie met behulp van Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 5a5f020b313029e84eae388bec3bb626a0331cb0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352045"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Quickstart: Aanroepen van uw Bing Custom Search-eindpunt met Node.js

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Lees in deze quickstart hoe u zoekresultaten opvraagt van de Bing Custom Search-instantie. Hoewel deze toepassing is geschreven in JavaScript, is de Bing Aangepaste zoekopdrachten-API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie voor meer informatie [Snelstart: Uw eerste Bing Custom Search-instantie maken](quick-start.md).

- [De Node.js JavaScript-runtime](https://www.nodejs.org/).

- De [JavaScript-aanvragenbibliotheek](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

- Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en voeg een instructie `require()` toe voor de aanvragenbibliotheek. Maak variabelen voor de abonnementssleutel, de aangepaste configuratie-id en een zoekterm.

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Een zoekaanvraag verzenden en ontvangen 

1. Maak een variabele voor het opslaan van de gegevens die in uw aanvraag worden verzonden. Stel de aanvraag-URL samen door uw zoekterm toe te voegen aan de queryparameter `q=`, en de aangepaste configuratie-id van uw zoekinstantie aan de parameter `customconfig=`. Scheid de parameters van elkaar met een en-teken (`&`). U kunt het globale eindpunt in de volgende code gebruiken of het eindpunt voor een [aangepast subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Gebruik de JavaScript-aanvragenbibliotheek voor het verzenden van een zoekaanvraag naar uw Bing Custom Search-exemplaar en geef informatie weer over de resultaten, waaronder de naam, de URL en de datum waarop de webpagina voor het laatst is verkend.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](./tutorials/custom-search-web-page.md)