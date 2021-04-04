---
title: De volg orde gebruiken om antwoorden weer te geven-Bing Entity Search
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van een classificatie om de antwoorden weer te geven die de Bing Entiteiten zoeken-API retourneert.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94365632"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Classificatie gebruiken om de zoek resultaten van entiteiten weer te geven  

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Elk zoek antwoord van de entiteit bevat een [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) -antwoord dat aangeeft hoe zoek resultaten moeten worden weer gegeven die worden geretourneerd door de Bing entiteiten zoeken-API. De volg orde van de antwoord groepen resulteert in een stip-, Mainline-en Sidebar-inhoud. Het hengel resultaat is het belangrijkste of prominente resultaat en moet eerst worden weer gegeven. Als u de resterende resultaten niet weer geven in een traditionele Mainline-en Sidebar-indeling, moet u de mainline-inhoud beter zichtbaar maken dan de inhoud van de zijbalk. 
  
Binnen elke groep identificeert de [items](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matrix de volg orde waarin de inhoud moet worden weer gegeven. Elk item biedt twee manieren om het resultaat binnen een antwoord te identificeren.  
 

|Veld | Description  |
|---------|---------|
|`answerType` en `resultIndex` | `answerType` identificeert het antwoord (ofwel entiteit of plaats) en `resultIndex` identificeert een resultaat binnen dat antwoord (bijvoorbeeld een entiteit). De index begint bij 0.|
|`value`    | `value` Bevat een ID die overeenkomt met de ID van een antwoord of een resultaat binnen het antwoord. Het antwoord of de resultaten bevatten de ID, maar niet beide. |
  
Met de `answerType` en `resultIndex` is een proces dat uit twee stappen bestaat. Gebruik eerst `answerType` om het antwoord te identificeren dat de resultaten bevat die moeten worden weer gegeven. Gebruik vervolgens `resultIndex` om de resultaten van dat antwoord te indexeren om het resultaat weer te geven. (De `answerType` waarde is de naam van het veld in het object [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) .) Als u wilt dat alle antwoord resultaten samen worden weer gegeven, bevat het positie-antwoord item niet het `resultIndex` veld.

Als u de ID gebruikt, moet u de classificatie-ID overeenkomen met de ID van een antwoord of een van de resultaten. Als een antwoord object een `id` veld bevat, worden alle resultaten van het antwoord samen weer gegeven. Als het-object bijvoorbeeld `Entities` het veld bevat `id` , geeft alle artikelen van de entiteit samen. Als het `Entities` veld niet is opgenomen in het object `id` , bevat elke entiteit een `id` veld en wordt de rang orde van de entiteiten mixen met de locatie resultaten.  
  
## <a name="ranking-response-example"></a>Voor beeld van een rang orde-antwoord

Hieronder ziet u een voor beeld van een [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Op basis van deze positie reactie worden de twee entiteits resultaten weer gegeven die gerelateerd zijn aan Jimi Hendrix.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](tutorial-bing-entities-search-single-page-app.md)