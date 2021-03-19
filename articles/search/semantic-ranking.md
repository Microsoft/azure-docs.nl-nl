---
title: Semantische classificatie
titleSuffix: Azure Cognitive Search
description: Hierin wordt het algoritme voor semantische classificatie in Cognitive Search beschreven.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604284"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantische classificatie in azure Cognitive Search

> [!IMPORTANT]
> Semantische zoek functies bevinden zich in de open bare preview-versie, die alleen beschikbaar is via de preview-REST API. Preview-functies worden aangeboden als-is, onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)en zijn niet gegarandeerd dezelfde implementatie bij algemene Beschik baarheid. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

Semantische classificatie is een uitbrei ding van de pijp lijn voor het uitvoeren van query's die de nauw keurigheid verbetert en terughalen door de beste overeenkomsten van een eerste resultatenset te herordenen. Semantische classificatie wordt ondersteund door geavanceerde diep gaande computer Lees vaardigheids modellen, getraind voor query's in natuurlijke taal, in tegens telling tot taal kundige overeenkomsten met tref woorden. In tegens telling tot het [standaard classificatie algoritme voor gelijkenis](index-ranking-similarity.md), gebruikt de semantische rang orde de context en betekenis van woorden om de relevantie te bepalen.

## <a name="how-semantic-ranking-works"></a>Hoe semantische classificatie werkt

De semantische classificatie is zowel resource als tijd intensief. Voor het volt ooien van de verwerking binnen de verwachte latentie van een query bewerking, neemt het model alleen de bovenste 50 documenten op die zijn geretourneerd uit het standaard [classificatie algoritme voor gelijkenis](index-ranking-similarity.md). De resultaten van de eerste classificatie kunnen meer dan 50 overeenkomsten bevatten, maar alleen de eerste 50 zal op semantische volg orde worden afgestemd. 

Voor een semantische classificatie gebruikt het model zowel de Lees vaardigheid van de computer als het leren van de overdracht om de documenten opnieuw te scoren op basis van de bedoeling van de query.

### <a name="preparation-passage-extraction-phase"></a>Fase voor bereiding (door gang extractie)

Voor elk document in de eerste resultaten is er sprake van een uitpakkende oefening die de sleutel door gang identificeert. Dit is een Overweeg-oefening die inhoud reduceert naar een hoeveelheid die snel kan worden verwerkt.

1. Voor elk van de 50-documenten wordt elk veld in de para meter searchFields in opeenvolgende volg orde geëvalueerd. De inhoud van elk veld wordt samengevoegd tot één lange teken reeks. 

1. De lange teken reeks wordt vervolgens bijgesneden om ervoor te zorgen dat de totale lengte niet meer is dan 8.000 tokens. Daarom is het raadzaam om beknopte velden eerst te positioneren zodat ze in de teken reeks zijn opgenomen. Als u zeer grote documenten met tekst-zware velden hebt, worden alle items na de token limiet genegeerd.

1. Elk document wordt nu vertegenwoordigd door één lange teken reeks die Maxi maal 8.000 tokens bevat. Deze teken reeksen worden naar het samenvattings model verzonden, waardoor de teken reeks verder wordt beperkt. Het samenvattings model evalueert de lange teken reeks op basis van belang rijke zinnen of geeft aan dat het document het beste samenvatten of de vraag beantwoordt.

1. De uitvoer van deze fase is een bijschrift (en optioneel een antwoord). Het bijschrift heeft Maxi maal 128 tokens per document en wordt beschouwd als de meeste vertegenwoordiger van het document.

### <a name="scoring-and-ranking-phases"></a>Fasen van scores en rang schikkingen

In deze fase worden alle 50 bijschriften geëvalueerd om de relevantie te beoordelen.

1. Scores worden bepaald door elk bijschrift te evalueren voor conceptuele en semantische relevantie, ten opzichte van de opgegeven query.

   Het volgende diagram geeft een illustratie van wat ' semantische relevantie ' betekent. Houd rekening met het begrip "kapitaal", dat kan worden gebruikt in de context van Finance, Law, geografie of grammatica. Als een query termen uit dezelfde vector ruimte bevat (bijvoorbeeld ' kapitaal ' en ' investering '), krijgt een document dat ook tokens in hetzelfde cluster bevat, een Score van meer dan een die niet.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vector weergave voor context" border="true":::

1. De uitvoer van deze fase is een @search.rerankerScore toegewezen aan elk document. Zodra alle documenten zijn gescoord, worden ze weer gegeven in aflopende volg orde en opgenomen in de query reactie payload.

## <a name="next-steps"></a>Volgende stappen

Semantische classificatie wordt aangeboden voor de standaard lagen in bepaalde regio's. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie en om u aan te melden. Met een nieuw query type worden de rang schikking van de relevantie en de reactie structuren van semantisch zoeken ingeschakeld. [Maak een semantische query](semantic-how-to-query-request.md)om aan de slag te gaan.

U kunt ook een van de volgende artikelen door nemen voor gerelateerde informatie.

+ [Overzicht van semantisch zoeken](semantic-search-overview.md)
+ [Een semantisch antwoord retour neren](semantic-answers.md)