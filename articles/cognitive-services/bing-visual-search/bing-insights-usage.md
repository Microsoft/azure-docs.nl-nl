---
title: Voor beelden van Bing Insights-Bing Visual Search
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u voor beelden van hoe Bing Visual Search kunnen worden gebruikt en hoe u Image Insights kunt weer geven op Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: e0a3bdc7e68301c067b7e1ca80a3e708522e0df1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96493352"
---
# <a name="examples-of-bing-insights-usage"></a>Voor beelden van het gebruik van Bing Insights

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

In dit artikel vindt u voor beelden van hoe Bing kan worden gebruikt en hoe u Image Insights kunt weer geven op Bing.com.

## <a name="pagesincluding-insight-example"></a>PagesIncluding Insight-voor beeld

In het volgende voor beeld wordt een koppeling naar de eerste webpagina weer gegeven en kan de gebruiker de lijst met andere webpagina's die de afbeelding bevatten, uitvouwen en samen vouwen:

![Uitgevouwen pagina's, inclusief](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources Insight-voor beeld

Hier volgt een voor beeld van de manier waarop Bing in de installatie kopie winkel bronnen kan weer geven voor producten:

![Winkelbronnen](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch Insight-voor beeld

Hieronder ziet u hoe u met Bing visueel vergelijk bare afbeeldingen kunt weer geven (Zie **Verwante installatie kopieën** in het voor beeld):

![Visueel vergelijkbare afbeeldingen](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recepten Insight-voor beeld

Hieronder ziet u hoe u met Bing recepten kunt weer geven voor de levens middelen die in de afbeelding worden weer gegeven. In het voor beeld kan de gebruiker weten dat er recepten beschikbaar zijn:

![Recepten en pagina's met inbegrip van](./media/recipes-pages-including.PNG)

 En biedt de koppeling naar de recepten wanneer de gebruiker de lijst uitbreidt:

![Uitgebreide recepten pagina's, inclusief](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches Insight-voor beeld

Hieronder ziet u hoe u met Bing gerelateerde zoek acties kunt weer geven van installatie kopieën die door anderen zijn gemaakt. Als de gebruiker op de afbeelding klikt, wordt de gebruiker naar de pagina met zoek resultaten van de Bing.com/images voor die gerelateerde query geleid.

![Verwante Zoek opdrachten voor installatie kopieën](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Voor beeld van entiteits inzicht

Hieronder ziet u hoe Bing informatie kan weer geven over de entiteit (persoon, plaats of ding) die in de afbeelding wordt weer gegeven. Als de gebruiker op de entiteits koppeling klikt, wordt de gebruiker naar de pagina met zoek resultaten voor Bing.com voor de entiteit geleid:

![Entiteit weer gegeven in afbeelding](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Andere inzichten weer geven die de gebruiker kan verkennen

Hieronder ziet u hoe Bing andere informatie kan weer geven over de installatie kopie die de gebruiker kan verkennen.

![Andere inzichten over de installatie kopie verkennen](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Omsluitende vakken en HOTS pots

Niet-standaard labels bevatten het begrenzingsvak dat het gebied van de interesse in de afbeelding aangeeft waarop de tag van toepassing is. Als de volledige afbeelding niet wordt geïdentificeerd in het begrenzingsvak, gebruikt u het selectie kader om een hotspot op de afbeelding te maken. De gebruiker kan op de hot spot klikken om informatie te krijgen over de inhoud die is gevonden onder de hot spot (of rechthoek). Als de afbeelding bijvoorbeeld een geavanceerde afbeelding is, bevatten de resultaten mogelijk Tags (en selectie kaders) voor accessoires die worden weer gegeven in de afbeelding, zoals een ring, juwelen, scarfs, enzovoort. In het volgende voor beeld ziet u een hotspot rechthoek voor de zonnebril die in de afbeelding worden weer gegeven:

![Selectie kader en hot spot](./media/click-to-search.PNG)

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met uw eerste aanvraag, raadpleegt u de Quick starts:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)