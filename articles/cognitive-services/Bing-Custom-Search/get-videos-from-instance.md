---
title: Video's ophalen van uw aangepaste weer gave-Bing Aangepaste zoekopdrachten
titleSuffix: Azure Cognitive Services
description: Overzicht op hoog niveau over het gebruik van Bing Aangepaste zoekopdrachten om Video's op te halen uit uw aangepaste weer gave van het web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4e2bb14519f8ed4e3e4a0e9ac8800957b30229a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338415"
---
# <a name="get-videos-from-your-custom-view"></a>Video's ophalen uit uw aangepaste weer gave

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de zoek functie voor aangepaste Video's in Bing kunt u uw aangepaste zoek ervaring met Video's verrijken. Bij aangepast zoeken, kunt u (net als bij webresultaten) zoeken naar video's in de lijst websites van uw exemplaar. U kunt de Video's ophalen met behulp van de zoek-API van Bing Custom Video's of via de gehoste UI-functie. Het gebruik van de gehoste UI-functie is eenvoudig te gebruiken en wordt aanbevolen om uw zoek opdracht in een korte volg orde uit te voeren. Zie [uw gehoste UI-ervaring configureren](hosted-ui.md)voor meer informatie over het configureren van uw gehoste gebruikers interface voor het toevoegen van Video's.

Als u meer controle wilt over het weer geven van de zoek resultaten, kunt u de zoek-API van Bing Custom Video's gebruiken. Omdat het aanroepen van de API lijkt op het aanroepen van de Bing Video's zoeken-API, wordt met de kassa [Bing Video Search](../bing-video-search/overview.md) voor beelden van het aanroepen van de API. Maar voordat u dit doet, moet u vertrouwd raken met de [aangepaste Video's zoeken API-referentie](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) -inhoud. De belangrijkste verschillen zijn de ondersteunde query parameters (u moet de query parameter customConfig toevoegen) en het eind punt waarnaar u aanvragen verzendt.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->