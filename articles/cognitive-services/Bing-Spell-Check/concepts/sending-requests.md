---
title: Aanvragen verzenden naar de Bing Spellingcontrole-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over Bing Spellingcontrole-modi, -instellingen en andere informatie met betrekking tot de API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 761cc3908b677b129e85be442b7a593a38a367f9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341560"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Aanvragen verzenden naar de Bing Spellingcontrole-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Om een stuk tekst te controleren op spel- en grammaticafouten, verzendt u een GET-aanvraag naar het volgende eindpunt:  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
Voor de aanvraag moet het HTTPS-protocol worden gebruikt.

Het is raadzaam dat alle aanvragen afkomstig zijn van een server. Het distribueren van de sleutel als onderdeel van een clienttoepassing biedt een kwaadwillende gebruiker namelijk meer mogelijkheden om de sleutel te onderscheppen. Een server biedt ook een centraal upgradepunt voor toekomstige versies van de API.

De aanvraag moet de queryparameter [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text) bevatten, met daarin het te controleren stuk tekst. Hoewel dat optioneel is, moet in de aanvraag ook de [mkt](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt)-queryparameter worden opgegeven, ter aanduiding van de markt waar u de resultaten vandaan wilt halen. Zie [Queryparameters](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters) voor een lijst met optionele queryparameters, zoals `mode`. Alle waarden van queryparameter moeten als een URL zijn gecodeerd.  
  
De aanvraag moet de header [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey) bevatten. Hoewel dit optioneel is, wordt u aangeraden ook altijd de volgende headers op te geven. Deze headers helpen de Bing Spellingcontrole-API nauwkeurigere resultaten op te halen:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Zie [Headers](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers) voor een lijst met alle aanvraag- en antwoordheaders.

Bij het aanroepen van de Bing Spellingcontrole-API met behulp van JavaScript, voorkomen de ingebouwde beveiligingsfuncties van uw browser mogelijk dat u toegang krijgt tot de waarden van deze headers.

Om dit probleem op te lossen, kunt u de aanvraag voor de Bing Spellingcontrole-API uitvoeren via een CORS-proxy. Het antwoord van een dergelijke proxy heeft een `Access-Control-Expose-Headers` koptekst die antwoord headers filtert en deze beschikbaar maakt voor Java script.

U kunt eenvoudig een CORS-proxy installeren zodat de [zelfstudie-app](../tutorials/spellcheck.md) toegang krijgt tot de optionele clientheaders. Als u [Node.js](https://nodejs.org/en/download/) nog niet hebt, moet u dit eerst installeren. Voer vervolgens de volgende opdracht in een opdrachtprompt in.

```console
npm install -g cors-proxy-server
```

Wijzig vervolgens het Bing Spellingcontrole-API-eind punt in het HTML-bestand in: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/`

Start ten slotte de CORS-proxy met de volgende opdracht:

```console
cors-proxy-server
```

Laat het opdrachtvenster geopend terwijl u de zelfstudie-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitbreidbare sectie met HTTP-headers onder de zoekresultaten ziet u nu (onder andere) de `X-MSEdge-ClientID`-header en kunt u controleren of deze voor elke aanvraag gelijk is.

## <a name="example-api-request"></a>Voorbeeld van API-aanvraag

Hieronder ziet u een aanvraag waarin alle voorgestelde queryparameters en headers zijn opgenomen. Als dit de eerste keer is van u een van de Bing-API's aanroept, moet u de header met de client-id weglaten. Voeg de client-id alleen toe als u eerder een Bing-API hebt aangeroepen en Bing een client-id heeft geretourneerd voor de combinatie van gebruiker en apparaat. 
  
```http
GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Hieronder ziet u het antwoord op de vorige aanvraag. Het voorbeeld bevat ook de Bing-specifieke antwoordheaders.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wat is de Bing Spellingcontrole-API?](../overview.md)
- [Naslag informatie over Bing Spellingcontrole-API V7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)