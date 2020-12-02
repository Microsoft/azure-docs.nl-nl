---
title: Wat is de Bing Entiteiten zoeken-API?
titleSuffix: Azure Cognitive Services
description: Meer informatie over de Bing Entity Search-API om aan de hand van zoekquery's entiteiten en locaties te extraheren en zoeken.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2a3d971ce9a4f89555eb3ffa489f8b19172a4b83
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351466"
---
# <a name="what-is-bing-entity-search-api"></a>Wat is de Bing Entiteiten zoeken-API?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

De Bing Entiteiten zoeken-API verzendt een zoekquery naar Bing en haalt resultaten op die zowel entiteiten als plaatsen bevatten. Plaatsresultaten kunnen restaurants, hotels of andere lokale bedrijven zijn. Bing retourneert plaatsen als in de query de naam van een lokaal bedrijf is opgegeven, of als er om een type bedrijf wordt gevraagd (bijvoorbeeld restaurants in de buurt). Bing retourneert entiteiten als in de query wordt verwezen naar bekende personen, plaatsen (toeristische attracties, provincies, landen/regio's, enzovoort) of dingen.

|Functie  |Beschrijving  |
|---------|---------|
|[Zoeksuggesties in realtime](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Geef zoeksuggesties op die kunnen worden weergegeven als een vervolgkeuzelijst wanneer gebruikers beginnen te typen.       | 
| [Ondubbelzinnig maken van entiteiten](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Meerdere entiteiten opvragen voor query's met meerdere mogelijke betekenissen. |
| [Locaties zoeken](concepts/search-for-entities.md#find-places) | Zoeken naar lokale bedrijven en entiteiten en de bijbehorende gegevens retourneren.  |

## <a name="workflow"></a>Werkstroom

De Bing Entiteiten zoeken-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U kunt de service gebruiken met de REST API of de SDK.

1. Maak een [Account voor Cognitive Services-API](../cognitive-services-apis-create-account.md) met toegang tot de Bing Zoeken-API's. Als u geen Azure-abonnement hebt, kunt u gratis [een account maken](https://azure.microsoft.com/free/cognitive-services/).
2. Verzend een aanvraag naar de API met een geldige zoekquery.
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

* Probeer de [interactieve demo](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) van de Bing Entiteiten zoeken-API. 
* Volg een [quickstart](quickstarts/csharp.md) om snel aan de slag te gaan met uw eerste aanvraag.
* De [naslaghandleiding over de Bing Entiteiten zoeken-API versie 7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* In de [Bing-vereisten voor gebruik en weergave](../bing-web-search/use-display-requirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Zoeken-API's.
* Bezoek de [hubpagina voor de Bing Search-API](../bing-web-search/overview.md) om de andere beschikbare API's te verkennen.