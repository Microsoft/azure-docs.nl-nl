---
title: Afbeeldingen ophalen uit uw aangepaste weer gave-Bing Aangepaste zoekopdrachten
titleSuffix: Azure Cognitive Services
description: Overzicht op hoog niveau over het gebruik van Bing Aangepaste zoekopdrachten om installatie kopieën op te halen uit uw aangepaste weer gave van het web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5025a68030f5dc3aec07d33af3f98370c8d64b87
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338466"
---
# <a name="get-images-from-your-custom-view"></a>Afbeeldingen ophalen uit uw aangepaste weer gave

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met Bing aangepaste afbeeldingen zoeken kunt u uw aangepaste zoek ervaring verrijken met afbeeldingen. Bij aangepast zoeken, kunt u (net als bij webresultaten) zoeken naar afbeeldingen in de lijst websites van uw exemplaar. U kunt de installatie kopieën ophalen met behulp van de zoek-API voor de aangepaste installatie kopieën van Bing of via de gehoste UI-functie. Het gebruik van de gehoste UI-functie is eenvoudig te gebruiken en wordt aanbevolen om uw zoek opdracht in een korte volg orde uit te voeren.  Zie [uw gehoste UI-ervaring configureren](hosted-ui.md)voor meer informatie over het configureren van uw gehoste gebruikers interface voor het toevoegen van installatie kopieën.

Als u meer controle wilt over het weer geven van de zoek resultaten, kunt u de zoek-API van Bing aangepaste installatie kopieën gebruiken. Omdat het aanroepen van de API lijkt op het aanroepen van de Bing Afbeeldingen zoeken-API, wordt met de kassa [Bing Image Search](../Bing-Image-Search/overview.md) voor beelden van het aanroepen van de API. Maar voordat u dit doet, moet u vertrouwd raken met de naslag informatie voor het [zoeken naar aangepaste afbeeldingen](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) in de API. De belangrijkste verschillen zijn de ondersteunde query parameters (u moet de query parameter customConfig toevoegen) en het eind punt waarnaar u aanvragen verzendt.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->