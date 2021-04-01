---
title: Zoek resultaten filteren-Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: U kunt filteren op de typen antwoorden die Bing bevat in het antwoord (bijvoorbeeld afbeeldingen, Video's en nieuws) met behulp van de query parameter ' responseFilter '.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 571314009b6f58e5c2ab6aac02cfebc82c53f42f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351858"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>De antwoorden filteren die het zoek antwoord bevat  

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Wanneer u een query op het web uitvoert, retourneert Bing alle relevante inhoud die wordt gevonden voor de zoek opdracht. Als de zoek query bijvoorbeeld ' zeil-en dinghies ' is, kan het antwoord de volgende antwoorden bevatten:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Queryparameters

Als u de antwoorden wilt filteren die worden geretourneerd door Bing, gebruikt u de onderstaande query parameters bij het aanroepen van de API.  

### <a name="responsefilter"></a>ResponseFilter

U kunt filteren op de typen antwoorden die Bing bevat in het antwoord (bijvoorbeeld afbeeldingen, Video's en nieuws) door gebruik te maken van de [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) -query parameter, een door komma's gescheiden lijst met antwoorden. Er wordt een antwoord opgenomen in het antwoord als Bing relevante inhoud voor de zoek actie vindt. 

Voor het uitsluiten van specifieke antwoorden van het antwoord, zoals afbeeldingen, laten voorafgaan door een `-` teken naar het antwoord type. Bijvoorbeeld:

```
&responseFilter=-images,-videos
```

Hieronder ziet u hoe u kunt gebruiken `responseFilter` om afbeeldingen, Video's en nieuws van zeil dinghies aan te vragen. Wanneer u de query reeks codeert, veranderen de komma's in% 2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Hieronder ziet u de respons op de vorige query. Omdat Bing geen relevante video-en nieuws resultaten heeft gevonden, worden deze niet in het antwoord vermeld.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Bing heeft geen video-en nieuws resultaten in het vorige antwoord geretourneerd, maar dit betekent niet dat de inhoud van de video en het nieuws niet bestaat. Het betekent gewoon dat de pagina deze niet bevat. Als u echter door meer resultaten [bladert](./paging-search-results.md) , zullen de volgende pagina's waarschijnlijk deze bevatten. Als u de [VIDEO'S zoeken API](../bing-video-search/overview.md) en [Nieuws zoeken API](../bing-news-search/search-the-web.md) -eind punten rechtstreeks aanroept, bevat het antwoord waarschijnlijk ook resultaten.

Het gebruik van kan worden gebruikt `responseFilter` om resultaten van één API te verkrijgen. Als u inhoud van één Bing-API wilt, moet u die API rechtstreeks aanroepen. Als u bijvoorbeeld alleen installatie kopieën wilt ontvangen, verzendt u een aanvraag naar het eind punt van de Afbeeldingen zoeken-API `https://api.cognitive.microsoft.com/bing/v7.0/images/search` of een van de andere eind punten van de [installatie kopieën](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) . Het aanroepen van de enkelvoudige API is niet alleen belang rijk voor de prestaties, maar omdat de leveranciersspecifieke Api's rijkere resultaten bieden. U kunt bijvoorbeeld filters gebruiken die niet beschikbaar zijn voor de Webzoekopdrachten-API om de resultaten te filteren.  

### <a name="site"></a>Site

Als u zoek resultaten wilt ophalen uit een specifiek domein, neemt u de `site:` query parameter op in de query teken reeks.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Afhankelijk van de query, kunt u, als u de `site:` query operator gebruikt, de kans bestaat dat het antwoord inhoud voor volwassenen bevat, ongeacht de instelling van [SafeSearch](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) . Gebruik `site:` alleen als u zich bewust bent van de inhoud op de site en uw scenario de mogelijkheid van inhoud voor volwassenen ondersteunt.

### <a name="freshness"></a>Nieuwheid

Als u de resultaten van webantwoorden wilt beperken tot webpagina's die Bing hebben gedetecteerd tijdens een bepaalde periode, stelt u de query parameter voor [vernieuwen](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) in op een van de volgende niet-hoofdletter gevoelige waarden:

* `Day` — Webpagina's retour neren die zijn gedetecteerd in de afgelopen 24 uur
* `Week` — Webpagina's retour neren die zijn gedetecteerd in de afgelopen 7 dagen
* `Month` — Webpagina's retour neren die in de afgelopen 30 dagen zijn gedetecteerd

U kunt deze para meter ook instellen op een aangepast datum bereik in het formulier `YYYY-MM-DD..YYYY-MM-DD` . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Als u de resultaten wilt beperken tot één datum, stelt u de para meter versheid in op een specifieke datum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

De resultaten kunnen webpagina's bevatten die buiten de opgegeven periode vallen als het aantal webpagina's dat Bing overeenkomt met uw filter criteria, kleiner is dan het aantal webpagina's dat u hebt aangevraagd (of het standaard nummer dat door Bing wordt geretourneerd).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Het aantal antwoorden in het antwoord beperken

Bing kan meerdere antwoord typen retour neren in het JSON-antwoord. Als u bijvoorbeeld query's gaat uitvoeren op *dinghies*, kan Bing retour neren `webpages` , `images` , `videos` en `relatedSearches` .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Als u het aantal antwoorden wilt beperken dat door Bing wordt teruggezet naar de twee beste antwoorden (webpagina's en afbeeldingen), stelt u de [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) -query parameter in op 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Het antwoord bevat alleen `webPages` en `images` .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Als u de `responseFilter` query parameter toevoegt aan de vorige query en deze instelt op webpaginas en nieuws, bevat het antwoord alleen webpagina's omdat nieuws niet wordt geclassificeerd.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Niet-geclassificeerde antwoorden promoten

Als de beste geclassificeerde antwoorden die Bing retourneert voor een query, zijn webpagina's, afbeeldingen, Video's en relatedSearches, bevat het antwoord deze antwoorden. Als u [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) instelt op twee (2), retourneert Bing de bovenste twee geclassificeerde antwoorden: webpagina's en installatie kopieën. Als u wilt dat Bing afbeeldingen en Video's in het antwoord bevat, geeft u de para meter voor de [niveau verhogen](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) op en stelt u deze in op afbeeldingen en Video's.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Hier volgt de reactie op de bovenstaande aanvraag. Bing retourneert de twee beste antwoorden, webpagina's en afbeeldingen en bevordert de video in het antwoord.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Als u `promote` op News hebt ingesteld, bevat het antwoord niet het nieuws antwoord omdat het geen geclassificeerd antwoord is &mdash; dat u alleen geclassificeerde antwoorden kunt promo veren.

De antwoorden die u wilt promoten, tellen niet mee voor de `answerCount` limiet. Als de geclassificeerde antwoorden bijvoorbeeld nieuws, afbeeldingen en Video's zijn, en u hebt ingesteld `answerCount` op 1 en `promote` Nieuws, bevat het antwoord nieuws en afbeeldingen. Of, als de geclassificeerde antwoorden Video's, afbeeldingen en nieuws zijn, bevat het antwoord Video's en nieuws.

U kunt `promote` alleen gebruiken als u de `answerCount` query parameter opgeeft.