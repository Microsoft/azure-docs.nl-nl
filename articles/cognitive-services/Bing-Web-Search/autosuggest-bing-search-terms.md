---
title: Zoek termen automatisch suggereren-Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: Koppel de Bing Webzoekopdrachten-API aan de Automatische suggestie-API voor Bing om gebruikers een betere Zoek ervaring te bieden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 1a3f479fbbe68c8880cd7fefb3b57c77d4cfbbfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349568"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatische suggestie voor zoek termen van Bing in uw toepassing

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Als u een zoekvak aanbiedt waarin de gebruiker een zoekterm invoert, kunt u de [Automatische suggestie-API voor Bing](../bing-autosuggest/get-suggested-search-terms.md) gebruiken om de ervaring te verbeteren. De API retourneert voorgestelde queryreeksen op basis van gedeeltelijke zoektermen terwijl de gebruiker typt.

Nadat de gebruiker een zoek term heeft ingevoerd, moet deze URL worden gecodeerd voordat de [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) query-para meter is ingesteld. Als de gebruiker bijvoorbeeld *sailing dinghies* invoert, stelt u `q` in op `sailing+dinghies` of `sailing%20dinghies`.

Als de query term een spel fout bevat, bevat het zoek antwoord een [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) -object. In het object worden de oorspronkelijke spelling weergegeven en de gecorrigeerde spelling die Bing heeft gebruikt voor de zoekopdracht.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

U kunt deze informatie gebruiken om de gebruiker te laten weten dat u de query reeks hebt gewijzigd wanneer u de zoek resultaten weergeeft.

![Query context UX-voor beeld](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Volgende stappen  

* Bekijk de voorbeeld [Bing webzoekopdrachten-API reacties](search-responses.md).  

## <a name="see-also"></a>Zie ook  

* [Bing Webzoekopdrachten-API referentie](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)