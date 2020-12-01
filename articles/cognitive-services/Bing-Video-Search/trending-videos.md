---
title: Op het web zoeken naar trending van Video's met behulp van de Bing Video's zoeken-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Bing Video's zoeken-API om op het web te zoeken naar trending-Video's.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: cfeebcda201df592f6c396dcc780208a36d1e989
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353796"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Bekijk trends in de video met de Bing Video's zoeken-API 

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Video's zoeken-API kunt u de Video's van de huidige trend in het web en in verschillende categorieën zoeken. 

## <a name="get-request"></a>GET-aanvraag

Als u de Video's van de huidige trend wilt ophalen uit de Bing Video's zoeken-API, verzendt u de volgende GET-aanvraag:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Markt ondersteuning

De volgende markten bieden ondersteuning voor trending van Video's.  
 
-   en-AU (Engels, Australië)  
-   en-CA (Engels, Canada)  
-   en-GB (Engels, Groot-Brittannië)  
-   en-ID (Engels, Indonesië)  
-   en-IE (Engels, Ierland)  
-   en-IN (Engels, India)  
-   en-NZ (Engels, Nieuw-Zeeland)  
-   en-PH (Engels, Filipijnen)  
-   en-AG (Engels, Singapore)  
-   en-US (Engels, Verenigde Staten)  
-   en-WW (Engelse, wereld wijd gecombineerde code)  
-   en-ZA (Engels, Zuid-Afrika)  
-   zh-CN (Chinees, China)

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord  

In het volgende voor beeld ziet u een API-antwoord met trending-Video's, die worden vermeld op categorie en subcategorie. Het antwoord bevat ook banner Video's, wat de populairste trending Video's zijn, en kan afkomstig zijn uit een of meer categorieën.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer inzicht krijgen dankzij video](video-insights.md)