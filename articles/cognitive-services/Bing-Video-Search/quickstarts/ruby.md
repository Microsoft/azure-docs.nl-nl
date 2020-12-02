---
title: "Snelstart: Video's zoeken met de REST API en Ruby - Bing Video Search"
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om zoekaanvragen voor video's met Ruby naar de REST-API van Bing Video's zoeken te verzenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.openlocfilehash: 00317dc694ba7ffedc668b3658be49f85a093532
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353813"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-ruby"></a>Snelstart: Video's zoeken met de REST-API van Bing Video's zoeken en Ruby

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om uw eerste aanroep naar de Bing Video’s zoeken-API te maken. Deze eenvoudige Ruby-toepassing stuurt een HTTP-videozoekquery naar de API en geeft het JSON-antwoord weer. Hoewel deze toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. 

De broncode voor dit voorbeeld is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingVideoSearchv7.rb) met extra foutafhandeling en codeaantekeningen.

## <a name="prerequisites"></a>Vereisten

* Ruby 2.4 of hoger

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Importeer de volgende pakketten in uw codebestand:

    ```ruby
    require 'net/https'
    require 'uri'
    require 'json'
    ```

2. Maak variabelen voor het API-eindpunt, het zoekpad voor de video-API, uw abonnementssleutel en de zoekterm. Voor de `url`-waarde kunt u het globale eindpunt in de volgende code gebruiken of het eindpunt voor het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat voor uw resource wordt weergegeven in de Azure-portal.

    ```ruby
    uri  = "https://api.cognitive.microsoft.com"
    path = "/bing/v7.0/videos/search"
    term = "kittens"
    accessKey = "your-subscription-key" 
    ```

## <a name="create-and-send-an-api-request"></a>Een API-aanvraag maken en verzenden

1. Gebruik de variabelen uit de vorige stap om een zoek-URL voor de aanvraag op te maken. Combineer uw URI en het pad, en pas daarna URL-codering toe op de zoekterm voordat u deze toevoegt aan de parameter `?q=`.

    ```ruby
    uri = URI(uri + path + "?q=" + URI.escape(term))
    ```

2. Voeg de volledige zoek-URL toe aan de aanvraag en voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.
    
    ``` ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = accessKey
    ```

3. Verzend de aanvraag en sla daarna het antwoord op.
    
    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

## <a name="process-and-view-the-response"></a>De reactie verwerken en weergeven

Nadat het antwoord is ontvangen, drukt u het JSON-antwoord af.

```ruby
puts JSON::pretty_generate(JSON(response.body))
```

## <a name="json-response"></a>JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

 [Wat is de Bing Video's zoeken-API?](../overview.md)