---
title: Hoe kan ik de zoek resultaten weer geven met behulp van classificaties-Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van een classificatie om Zoek resultaten van de Bing Webzoekopdrachten-API weer te geven.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 20501d0993cc4566a79d6e916d801911606bea35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94380446"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>De classificatie gebruiken om Bing Webzoekopdrachten-API resultaten weer te geven  

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Elk zoek antwoord bevat een [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) -antwoord dat aangeeft hoe de zoek resultaten moeten worden weer gegeven. De rang orde van antwoord groepen resulteert in Mainline inhoud en Sidebar-inhoud voor een traditionele pagina met zoek resultaten. Als de resultaten niet worden weer gegeven in een traditionele Mainline-en Sidebar-indeling, moet u de mainline-inhoud beter zichtbaar maken dan de inhoud van de zijbalk.  

Binnen elke groep (mainline of Sidebar) identificeert de [items](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) matrix de volg orde waarin de inhoud moet worden weer gegeven. Elk item biedt de volgende twee manieren om het resultaat binnen een antwoord te identificeren.  

-   `answerType` en `resultIndex` het `answerType` veld geeft het antwoord aan (bijvoorbeeld webpagina of nieuws) en `resultIndex` identificeert een resultaat binnen het antwoord (bijvoorbeeld een nieuws artikel). De index is op basis van nul.  

-   `value` : Het `value` veld bevat een id die overeenkomt met de id van een antwoord of een resultaat binnen het antwoord. Het antwoord of de resultaten bevatten de ID, maar niet beide.  

Het gebruik van de ID is eenvoudiger te gebruiken omdat u alleen de classificatie-ID moet overeenkomen met de ID van een antwoord of een van de bijbehorende resultaten. Als een antwoord object een `id` veld bevat, worden alle resultaten van het antwoord samen weer gegeven. Als het `News` object bijvoorbeeld het `id` veld bevat, worden alle nieuws artikelen samen weer gegeven. Als het `News` veld niet is opgenomen in het `id` -object, bevat elk nieuws artikel een `id` veld en de rang orde van het bericht wordt de nieuws artikelen gemixen met de resultaten van andere antwoorden.  

Het gebruik van `answerType` en `resultIndex` is iets gecompliceerder. U gebruikt `answerType` om het antwoord te identificeren dat de resultaten bevat die moeten worden weer gegeven. Vervolgens gebruikt u `resultIndex` voor het indexeren van de resultaten van het antwoord om het resultaat weer te geven. (De `answerType` waarde is de naam van het veld in het object [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) .) Als u wilt dat alle antwoord resultaten samen worden weer gegeven, bevat het positie-antwoord item niet het `resultIndex` veld.  

## <a name="ranking-response-example"></a>Voor beeld van een rang orde-antwoord

Hieronder ziet u een voor beeld van een [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Omdat het webantwoord geen `id` veld bevat, moet u alle webpagina's afzonderlijk weer geven op basis van de rang schikking (elke webpagina bevat een `id` veld). En omdat de antwoorden van afbeeldingen, Video's en verwante Zoek opdrachten het `id` veld bevatten, kunt u de resultaten van elk van deze antwoorden weer geven op basis van de rang schikking.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Op basis van deze prioriteits reactie worden de volgende zoek resultaten weer gegeven in de mainline:  

-   Het resultaat van de eerste webpagina
-   Alle installatie kopieën  
-   De tweede en derde webpagina resultaten  
-   Alle Video's  
-   De resultaten van de vierde, vijfde en zesde webpagina  

Op de zijbalk worden de volgende zoek resultaten weer gegeven:  

-   Alle verwante Zoek opdrachten  


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het promo veren van [niet-geclassificeerde resultaten voor het promo veren van de antwoorden die niet worden gerangschikt](./filter-answers.md#promoting-answers-that-are-not-ranked).

Zie [het aantal antwoorden in het antwoord beperken](./filter-answers.md#limiting-the-number-of-answers-in-the-response)voor informatie over het beperken van het aantal geclassificeerde antwoorden in het antwoord.

Zie [c#-classificatie-zelf studie](./csharp-ranking-tutorial.md)voor een c#-voor beeld waarbij gebruik wordt gemaakt van een classificatie om resultaten weer te geven.