---
title: Wat is Bing Visual Search-API?
titleSuffix: Azure Cognitive Services
description: Bing Visual Search biedt details of inzichten over een afbeelding, zoals vergelijkbare afbeeldingen of winkelbronnen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 7dfc704fb38550993adb7835d4500dee890117a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486977"
---
# <a name="what-is-the-bing-visual-search-api"></a>Wat is Bing Visual Search-API?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Bing Visual Search-API retourneert inzichten over een afbeelding. U kunt een afbeelding uploaden of een URL naar een afbeelding opgeven. Inzichten zijn visueel vergelijkbare afbeeldingen, winkelbronnen, webpagina's die de afbeelding bevatten, enzovoort. Inzichten die door de Bing Visual Search-API worden geretourneerd, zijn vergelijkbaar met die van Bing.com/images. 

Als u de [ Bing Afbeeldingen zoeken-API](../bing-image-search/overview.md) gebruikt, kunt u inzichttokens gebruiken die zijn bijgevoegd bij de zoekresultaten van Bing Visual Search, in plaats van dat u een afbeelding uploadt.

> [!IMPORTANT]
> Als u afbeeldingsinzichten ophaalt met behulp van Bing Afbeeldingen zoeken-API, kunt u overwegen om over te schakelen naar Bing Visual Search-API, omdat deze uitgebreidere inzichten biedt.

## <a name="insights"></a>Inzichten

U kunt de volgende inzichten ontdekken met behulp van Bing Visual Search:

| Inzicht                              | Beschrijving |
|--------------------------------------|-------------|
| Visueel vergelijkbare afbeeldingen              | Een lijst met afbeeldingen die visueel vergelijkbaar zijn met de ingevoerde afbeelding. |
| Visueel vergelijkbare producten            | Producten die visueel vergelijkbaar zijn met het weergegeven product.            |
| Winkelbronnen                     | Een lijst met plaatsen waar u het artikel kunt kopen dat wordt weergegeven op de ingevoerde afbeelding.            |
| Verwante zoekopdrachten                     | Een lijst met verwante zoekopdrachten die door anderen zijn uitgevoerd, of die zijn gebaseerd op de inhoud van de afbeelding.            |
| Webpagina's die de afbeelding bevatten     | Webpagina's die de ingevoerde afbeelding bevatten.            |
| Recepten                              | Een lijst met webpagina's met recepten voor het maken van het gerecht dat wordt weergegeven op de ingevoerde afbeelding.            |
| Entiteiten                             | Bekende personen, plaatsen en dingen. |

Naast deze inzichten retourneert Bing Visual Search ook diverse termen (tags) die zijn afgeleid van de ingevoerde afbeelding. Aan de hand van deze tags kunnen gebruikers in de afbeelding gedetecteerde concepten verkennen. Als de ingevoerde afbeelding bijvoorbeeld van een beroemde atleet is, kan een van de tags de naam van de atleet zijn, een andere tag kan Sport zijn. Of als de ingevoerde afbeelding een appeltaart is, kunnen de tags Appeltaart, Taarten, Toetjes zijn.

De resultaten van Bing Visual Search bevatten ook begrenzingsvakken voor interessegebieden in de afbeelding. Als de afbeelding bijvoorbeeld meerdere beroemdheden bevat, kunnen de resultaten begrenzingsvakken bevatten voor elk van de herkende beroemdheden. Of, als Bing een product of kledingstuk herkent in de afbeelding, kan het resultaat een begrenzingsvak bevatten voor het herkende item.

## <a name="workflow"></a>Werkstroom

Bing Visual Search-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U kunt de REST API of de SDK gebruiken voor de service.

1. Maak een [account voor Cognitive Services](../cognitive-services-apis-create-account.md) met toegang tot de Bing Search-API's. Als u geen Azure-abonnement hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/cognitive-services/).
2. Verzend een aanvraag naar de API met een geldige zoekquery.
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Bekijk eerst de [interactieve demo](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) van de Bing Visual Search-API.
In de demo ziet u hoe u snel een zoekquery kunt aanpassen en internet kunt doorzoeken op afbeeldingen.

Zie deze quickstarts om snel aan de slag te gaan met uw eerste aanvraag:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>Zie ook

* De documentatie voor [Afbeeldingen - Visueel zoeken](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) bevat beschrijvingen van de definities en informatie over de eindpunten, aanvraagheaders, antwoorden en queryparameters waarmee u afbeeldingszoekresultaten kunt opvragen.

* In de [vereisten voor gebruik en weergave van de Bing Search-API](../bing-web-search/use-display-requirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Search-API's.

* Bezoek de [hubpagina voor de Bing Search-API](../bing-web-search/overview.md) om de andere beschikbare API's te verkennen.