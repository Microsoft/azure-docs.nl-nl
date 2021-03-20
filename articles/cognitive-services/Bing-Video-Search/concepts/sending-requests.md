---
title: Zoek aanvragen naar de Bing Video's zoeken-API verzenden
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie over de parameters en de kenmerken van aanvragen die naar de Bing Video's zoeken-API worden verzonden, evenals het JSON-antwoordobject dat wordt geretourneerd.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: aahi
ms.openlocfilehash: 78a3c3c2936bfcaf58ea9e46bd3d6a610cdfe436
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341492"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>Zoekaanvragen verzenden naar de Bing Video's zoeken-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

In dit artikel vindt u informatie over de parameters en de kenmerken van aanvragen die naar de Bing Video's zoeken-API worden verzonden, evenals het JSON-antwoordobject dat wordt geretourneerd. 

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Zoektermen voorstellen met de Bing Automatische suggestie-API

Als u een zoekvak aanbiedt waarin de gebruiker een zoekterm invoert, kunt u de [Automatische suggestie-API voor Bing](../../bing-autosuggest/get-suggested-search-terms.md) gebruiken om de ervaring te verbeteren. De API retourneert voorgestelde queryreeksen op basis van gedeeltelijke zoektermen terwijl de gebruiker typt.

Nadat de gebruiker de zoekterm heeft ingevoerd, past u URL-codering toe op de term voordat u de queryparameter [q](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) instelt. Als de gebruiker bijvoorbeeld *sailing dinghies* invoert, stelt u `q` in op `sailing+dinghies` of `sailing%20dinghies`.

## <a name="sending-a-request"></a>Een aanvraag verzenden

Om video's als zoekresultaat te krijgen, verstuurt u een GET-aanvraag naar het volgende eindpunt:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
Voor de aanvraag moet het HTTPS-protocol worden gebruikt.

Het is raadzaam dat alle aanvragen afkomstig zijn van een server. Het distribueren van de sleutel als onderdeel van een clienttoepassing biedt een kwaadwillende gebruiker namelijk meer mogelijkheden om de sleutel te onderscheppen. Als u ervoor kiest om alle aanroepen via een server te laten lopen, beschikt u bovendien over een centraal upgradepunt voor toekomstige versies van de API.

  
De aanvraag moet de parameter [q](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) bevatten, met daarin de zoekterm van de gebruiker. Hoewel dit optioneel is, moet de aanvraag ook de queryparameter [mkt](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt) bevatten. Hiermee geeft u de markt aan waarvan de resultaten afkomstig moeten zijn. Zie [Queryparameters](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters) voor een lijst met optionele queryparameters, zoals `pricing`. Alle waarden van queryparameter moeten als een URL zijn gecodeerd.  
  
De aanvraag moet de header [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey) bevatten. Hoewel dit optioneel is, wordt u aangeraden ook altijd deze headers op te geven:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

De headers ClientIP en Location zijn belangrijk voor het retourneren van locatiespecifieke inhoud.  

Zie [Headers](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers) voor een lijst met alle aanvraag- en antwoordheaders.

## <a name="example-search-request"></a>Voorbeeld van zoekaanvraag

Hieronder ziet u een zoekaanvraag waarin alle voorgestelde queryparameters en headers zijn opgenomen. Als dit de eerste keer is van u een van de Bing-API's aanroept, moet u de header met de client-id weglaten. Voeg de client-id alleen toe als u eerder een Bing-API hebt aangeroepen en Bing een client-id heeft geretourneerd voor de combinatie van gebruiker en apparaat. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Hieronder ziet u het antwoord op de vorige aanvraag. Het voorbeeld bevat ook de Bing-specifieke antwoordheaders.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

Probeer de API uit. Ga naar [Video Search API Testing Console](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

Zie [What is Bing Video Search?](../overview.md) (Wat is Bing Video's zoeken?) voor meer informatie over het gebruiken van de antwoordobjecten.

Zie [Inzichten over video's](../video-insights.md) voor meer informatie over het verkrijgen van inzichten over een video, zoals verwante zoekopdrachten.  
  
Zie [Trending video's ophalen](../trending-videos.md) voor meer informatie over video's die trending zijn op sociale media.