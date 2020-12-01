---
title: Down loads van trend afbeeldingen met de Bing Afbeeldingen zoeken-API
titleSuffix: Azure Cognitive Services
description: Zoek naar de huidige trend afbeeldingen op het web met de Bing Afbeeldingen zoeken-API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: cf7d1baf895d44730eb913b658ee4c7fe7eb7b11
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341611"
---
# <a name="get-trending-images-from-the-web"></a>Trend afbeeldingen ophalen van het web

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Verzend de volgende GET-aanvraag om de huidige trend afbeeldingen te downloaden:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

De API voor trending-installatie kopieën ondersteunt momenteel alleen de volgende markten:  

- en-US (Engels, Verenigde Staten)  
- en-CA (Engels, Canada)  
- en-AU (Engels, Australië)  
- zh-CN (Chinees, China)

Het antwoord bevat een [TrendingImages](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) -object waarin afbeeldingen per categorie worden weer gegeven. Gebruik de categorie `title` om de installatie kopieën in uw gebruikers ervaring te groeperen. De categorieën kunnen dagelijks worden gewijzigd.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Elke tegel bevat een afbeelding en opties voor het ophalen van gerelateerde installatie kopieën. Als u de gerelateerde installatie kopieën wilt ophalen, kunt u de query gebruiken `text` om de [afbeeldingen zoeken-API](./overview.md) aan te roepen en zelf de gerelateerde installatie kopieën weer te geven. U kunt ook de URL in gebruiken `webSearchUrl` om de pagina met zoek resultaten van de installatie kopie van Bing te maken. Deze bevat de gerelateerde installatie kopieën.

Als u de Afbeeldingen zoeken-API aanroept om de gerelateerde installatie kopieën op te halen, stelt u de [id-](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) query parameter in op de id in het `id` veld. Als u de ID opgeeft, zorgt u ervoor dat het antwoord de afbeelding bevat (de eerste afbeelding in het antwoord) en de bijbehorende installatie kopieën. Stel ook de para meter [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) query in op de tekst in het `query` veld van het object `text` .

In het volgende voor beeld ziet u hoe u de afbeeldings-ID gebruikt om gerelateerde installatie kopieën van Dhr. Smith te verkrijgen in de voor gaande trends in de API-reactie.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```