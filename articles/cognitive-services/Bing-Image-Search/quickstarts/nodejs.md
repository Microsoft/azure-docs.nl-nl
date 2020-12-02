---
title: 'Quickstart: Afbeeldingen zoeken met de REST API voor Bing Image Search en Node.js'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om aanvragen voor het zoeken van afbeeldingen naar de REST API voor Bing Image Search te verzenden met JavaScript en JSON-antwoorden.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: 847f61c66da44af7a644a549b6ea8466ddd464ef
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341883"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Quickstart: Afbeeldingen zoeken met de REST API voor Bing Image Search en Node.js

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om te leren hoe u zoekaanvragen verzendt naar de Bing Image Search-API. Deze JavaScript-toepassing verzendt een zoekquery naar de API en geeft de URL weer van de eerste afbeelding in de resultaten. Hoewel deze toepassing in JavaScript is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) met extra foutafhandeling en aantekeningen.

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).

* De [JavaScript-aanvragenbibliotheek](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Zie [Prijzen van Cognitive Services - Bing Search-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) voor meer informatie.

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en stel de vereisten voor striktheid en HTTPS in.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Maak variabelen voor het API-eindpunt, het zoekpad voor de afbeeldings-API, uw abonnementssleutel en de zoekterm. Voor `host` kunt u het volgende globale eindpunt of het eindpunt [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in Azure Portal voor uw resource.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Compileer de zoekopdracht en een query.

1. Gebruik de variabelen uit de laatste stap om een zoek-URL voor de API-aanvraag te formatteren. Versleutel uw zoekterm met een URL voordat u deze naar de API verzendt.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Gebruik de aanvraagbibliotheek om uw query naar de API te verzenden. 
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Het antwoord verwerken en parseren

1. Definieer een functie met de naam `response_handler` en als parameter de HTTP-aanroep `response`. 

2. Definieer in deze functie een variabele die de hoofdtekst van het JSON-antwoord bevat. 

    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Sla de hoofdtekst van het antwoord op wanneer de vlag `data` wordt aangeroepen.

    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. Wanneer de vlag `end` wordt weergegeven, krijgt u het eerste resultaat vanuit het JSON-antwoord. Druk de URL van de eerste afbeelding af, samen met het totale aantal geretourneerde afbeeldingen.

    ```javascript
    response.on('end', function () {
        let firstImageResult = imageResults.value[0];
        console.log(`Image result count: ${imageResults.value.length}`);
        console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
        console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
     });
    ```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Antwoorden die afkomstig zijn van de Bing Afbeeldingen zoeken-API, worden geretourneerd in de JSON-indeling. Dit voorbeeldantwoord is ingekort zodat één resultaat wordt weergegeven.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een app met één pagina maken](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zie ook

* [Wat is de Bing Image Search-API?](../overview.md)  
* [Online interactieve demo proberen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Prijsinformatie voor Bing Search-API's](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Documentatie voor Azure Cognitive Services](../../index.yml)
* [Naslag voor Bing Afbeeldingen zoeken-API](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)