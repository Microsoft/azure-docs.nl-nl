---
title: Geografische grenzen gebruiken om de resultaten van de Bing lokale zakelijke zoek-API te filteren
titleSuffix: Azure Cognitive Services
description: In dit artikel leest u hoe u zoek resultaten kunt filteren op basis van de Bing Local Business Search-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2e72abd777ac4834385fd916b730d438b8540203
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490258"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Geografische grenzen gebruiken om de resultaten van de Bing lokale zakelijke zoek-API te filteren

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Local Business Search-API kunt u grenzen instellen voor het specifieke geografische gebied waarin u wilt zoeken met behulp van de- `localCircularView` of- `localMapView` query parameters. Zorg ervoor dat u slechts één para meter gebruikt in uw query's. 

Als een zoek term een expliciete geografische locatie bevat, wordt deze door de Bing Local Business-API automatisch gebruikt om grenzen in te stellen voor de zoek resultaten. Als de zoek term bijvoorbeeld is `sailing in San Diego` , `San Diego` wordt gebruikt als de locatie en alle andere opgegeven locaties in de query parameters of gebruikers headers worden genegeerd. 

Als er geen geografische locatie wordt gedetecteerd in de zoek term en er geen geografische locatie is opgegeven met behulp van de query parameters, zal de Bing lokale zakelijke zoek-API proberen de locatie te bepalen van de aanvraag `X-Search-ClientIP` of de `X-Search-Location` kopteksten. Als geen van beide headers is opgegeven, bepaalt de API de locatie van ofwel het client-IP-adres van de aanvraag, ofwel GPS-coördinaten voor mobiele apparaten.

## <a name="localcircularview"></a>localCircularView

De `localCircularView` para meter creëert een cirkel vormige geografische regio rond een set breedte-en lengte graad-coördinaten, gedefinieerd door een RADIUS. Wanneer u deze para meter gebruikt, bevatten reacties van de Bing lokale Business Search-API alleen locaties in deze cirkel, in tegens telling tot de `localMapView` para meter die locaties kan bevatten die enigszins buiten het zoek gebied vallen.

Als u een circulair geografisch zoek gebied wilt opgeven, kiest u een breedte graad en lengte graad als midden van de cirkel en een straal in meters. Deze para meter kan vervolgens worden toegevoegd aan een query reeks, bijvoorbeeld: `q=Restaurants&localCircularView=47.6421,-122.13715,5000` .

Volledige query:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

De `localMapView` para meter geeft een rechthoekig geografisch gebied op dat u wilt zoeken, met behulp van twee sets coördinaten om de hoeken van de Zuid-en noordwesten op te geven. Wanneer u deze para meter gebruikt, kunnen reacties van de Bing lokale Business Search-API locaties bevatten binnen en alleen buiten het opgegeven gebied, in tegens telling tot de `localCircularView` para meter, die alleen locaties binnen het zoek gebied bevat.

Als u een rechthoekig zoek gebied wilt opgeven, moet u twee sets met Latitude/lengte graad-coördinaten gebruiken als het Zuid-en noordwesten van de grens. Zorg ervoor dat u eerst de Zuidoost-coördinaten definieert, zoals in het volgende voor beeld: `localMapView=47.619987,-122.181671,47.6421,-122.13715` .

Volledige query:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Volgende stappen
- [Lokale zakelijke zoek opdracht voor zoeken in Java Quick Start](quickstarts/local-search-java-quickstart.md)
- [Lokale zakelijke zoek acties C# Quick Start](quickstarts/local-quickstart.md)
- [Quick start voor lokaal bedrijfs Zoek knooppunt](quickstarts/local-search-node-quickstart.md)
- [Quickstart over zoeken naar lokale bedrijven met Python](quickstarts/local-search-python-quickstart.md)