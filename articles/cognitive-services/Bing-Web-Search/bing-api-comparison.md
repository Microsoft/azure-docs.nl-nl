---
title: Wat zijn de Bing Zoeken-API's?
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u meer informatie over de Bing Zoeken-API's en hoe u cognitieve Internet zoekopdrachten in uw apps en services kunt inschakelen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349534"
---
# <a name="what-are-the-bing-search-apis"></a>Wat zijn de Bing Zoeken-API's?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Search-API's kunt u web-apps en -services maken die webpagina's, afbeeldingen, nieuws, locaties en nog veel meer vinden zonder advertenties. Door Zoek aanvragen te verzenden met behulp van de Bing Search REST-Api's of Sdk's, kunt u relevante informatie en inhoud voor zoeken op het web ophalen. In dit artikel vindt u meer informatie over de verschillende Bing zoeken-Api's en over hoe u cognitieve Zoek opdrachten kunt integreren in uw toepassingen en services. Prijzen en frequentie limieten kunnen per Api's verschillen.

## <a name="the-bing-web-search-api"></a>De Bing Webzoekopdrachten-API

Het [Bing webzoekopdrachten-API](../Bing-Web-Search/overview.md) retourneert webpagina's, afbeeldingen, video, nieuws en meer. U kunt de Zoek opdrachten die zijn verzonden naar deze API filteren om bepaalde inhouds typen op te nemen of uit te sluiten.

Overweeg het gebruik van de Bing Webzoekopdrachten-API in toepassingen die mogelijk moeten zoeken naar alle typen relevante webinhoud. Als uw toepassing zoekt naar een specifiek type online-inhoud, kunt u een van de onderstaande Zoek-Api's overwegen:

## <a name="content-specific-bing-search-apis"></a>Leverancierspecifieke Zoek-Api's voor Bing

De volgende Bing Search-Api's retour neren specifieke inhoud van het web zoals afbeeldingen, nieuws, lokale bedrijven en Video's.

| Bing-API | Beschrijving |
| -- | -- |
| [Entiteiten zoeken](../Bing-Entities-Search/overview.md) | Het Bing Entiteiten zoeken-API retourneert Zoek resultaten met entiteiten, die mensen, plaatsen of dingen kunnen zijn. Afhankelijk van de query retourneert de API een of meer entiteiten die voldoen aan de zoek query. De zoek query kan bestaan uit verhalende individuen, lokale bedrijven, bezienswaardigheden, doelen en meer. |
| [Afbeeldingen zoeken](../Bing-Image-Search/overview.md) | Met de Bing Afbeeldingen zoeken-API kunt u zoeken naar en zoeken naar statische en bewegende afbeeldingen van hoge kwaliteit, vergelijkbaar met [Bing.com/images](https://www.Bing.com/images). U kunt Zoek opdrachten verfijnen om afbeeldingen op te nemen of uit te sluiten op basis van het kenmerk, inclusief grootte, kleur, licentie en versheid. U kunt ook zoeken naar trend afbeeldingen, afbeeldingen uploaden om inzicht te krijgen in en miniatuur weergaven weer geven. |
| [Nieuws zoeken](../Bing-News-Search/search-the-web.md) | Met de Bing Nieuws zoeken-API kunt u nieuws artikelen vinden die vergelijkbaar zijn met [Bing.com/News](https://www.Bing.com/news). De API retourneert nieuws artikelen uit een of meer bronnen of specifieke domeinen. U kunt zoeken in verschillende categorieën om trending artikelen, belang rijke verhalen en nieuwskoppen op te halen. |
| [Video's zoeken](../Bing-Video-Search/overview.md) | Met de Bing Video's zoeken-API kunt u Video's op het web vinden. Bekijk Video's, gerelateerde inhoud en miniatuur voorvertoningen. |
| [Visual Search](../Bing-visual-search/overview.md) | Upload een afbeelding of gebruik een URL om informatie hierover te verkrijgen, zoals visueel soort gelijke producten, afbeeldingen en gerelateerde zoek opdrachten. |
 [Lokale zakelijke zoek opdracht](../bing-local-business-search/overview.md) | Met de Bing Local Business Search-API kunnen uw toepassingen contact gegevens en locatie-informatie vinden over lokale bedrijven op basis van zoek query's. |

## <a name="the-bing-custom-search-api"></a>De Bing Custom Search-API

Als u een aangepaste zoek instantie met de [Bing aangepaste zoekopdrachten](../Bing-Custom-Search/overview.md) -API maakt, kunt u een zoek ervaring maken die alleen gericht is op inhoud en onderwerpen die u vindt. Nadat u bijvoorbeeld de domeinen, websites en specifieke webpagina's hebt opgegeven waarop Bing wordt gezocht, worden de resultaten door Bing Aangepaste zoekopdrachten op die specifieke inhoud aangepast. U kunt de Bing aangepaste automatische suggestie-, afbeeldings-en Video's zoeken-Api's opnemen om uw zoek ervaring verder aan te passen.

## <a name="additional-bing-search-apis"></a>Aanvullende Bing Zoeken-API's

Met de volgende Bing Zoeken-API's kunt u uw zoek ervaring verbeteren door ze te combi neren met andere Bing zoeken-Api's.

| API | Beschrijving |
| -- | -- |
| [Bing Automatische suggesties](../Bing-Autosuggest/get-suggested-search-terms.md) | Verbeter de zoek ervaring van uw toepassing met de Automatische suggestie-API voor Bing door voorgestelde Zoek opdrachten in realtime te retour neren.  |
| [Bing-statistieken](bing-web-stats.md) | Bing-statistieken bieden analyses voor de Bing Zoeken-API's uw toepassing gebruikt. Enkele van de beschik bare analyses zijn oproep volume, Top query reeksen en geografische distributie. |

## <a name="next-steps"></a>Volgende stappen

* [Prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) voor Bing Search-API
* In de [Bing-vereisten voor gebruik en weergave](./use-display-requirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Zoeken-API's.