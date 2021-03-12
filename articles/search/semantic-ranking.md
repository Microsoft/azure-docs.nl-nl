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
ms.openlocfilehash: a008551ac6f149617feedd01e256b637f83e975d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103235015"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantische classificatie in azure Cognitive Search

> [!IMPORTANT]
> Semantische zoek functies bevinden zich in de open bare preview-versie, die alleen beschikbaar is via de preview-REST API. Preview-functies worden aangeboden als-is, onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)en zijn niet gegarandeerd dezelfde implementatie bij algemene Beschik baarheid. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

Semantische classificatie is een uitbrei ding van de pijp lijn voor het uitvoeren van query's die de nauw keurigheid verbetert en terughalen door de beste overeenkomsten van een eerste resultatenset te herordenen. Semantische classificatie wordt ondersteund door geavanceerde diep gaande computer Lees vaardigheids modellen, getraind voor query's in natuurlijke taal, in tegens telling tot taal kundige overeenkomsten met tref woorden. In tegens telling tot het [standaard classificatie algoritme voor gelijkenis](index-ranking-similarity.md), gebruikt de semantische rang orde de context en betekenis van woorden om de relevantie te bepalen.

## <a name="how-semantic-ranking-works"></a>Hoe semantische classificatie werkt

De semantische classificatie is zowel resource als tijd intensief. Voor het volt ooien van de verwerking binnen de verwachte latentie van een query bewerking, neemt het model alleen de bovenste 50 documenten op die zijn geretourneerd uit het standaard [classificatie algoritme voor gelijkenis](index-ranking-similarity.md). De resultaten van de eerste classificatie kunnen meer dan 50 overeenkomsten bevatten, maar alleen de eerste 50 zal op semantische volg orde worden afgestemd. 

Voor een semantische classificatie gebruikt het model zowel de Lees vaardigheid van de computer als het leren van de overdracht om de documenten opnieuw te scoren op basis van de bedoeling van de query.

1. Voor elk document evalueert de semantische rangorde de velden in de para meter searchFields om de inhoud te consolideren in één grote teken reeks.

1. De teken reeks wordt vervolgens bijgesneden om ervoor te zorgen dat de totale lengte niet meer is dan 20.000 tokens. Als u zeer grote documenten hebt, met een inhouds veld of merged_content veld met veel pagina's met inhoud, worden alleen de eerste 20.000-tokens gebruikt.

1. Elk van de 50-documenten wordt nu vertegenwoordigd door één lange teken reeks die Maxi maal 20.000 tokens is. Deze teken reeks wordt naar het samenvattings model verzonden. Het samenvattings model produceert bijschriften (en antwoorden) met behulp van de Lees vaardigheid van de machine om gangen te identificeren die worden weer gegeven om de inhoud te samenvatten of de vraag te beantwoorden. De uitvoer van het samenvattings model is een verder verlaagde teken reeks met Maxi maal 128 tokens.

1. De kleinere teken reeks wordt het bijschrift van het document. het vertegenwoordigt de meest relevante gangen die worden gevonden in de grotere teken reeks. De set met ondertiteling van 50 (of minder) wordt vervolgens gerangschikt op volg orde van relevantie. 

De conceptuele en semantische relevantie wordt vastgesteld door middel van Vector weergave en term clusters. Terwijl een algoritme voor het vergelijken van tref woorden een gelijk gewicht van een wille keurige term in de query kan geven, is het semantische model getraind om de onderlinge afhankelijkheid en relaties tussen woorden te herkennen die anderszins niet gerelateerd zijn aan het Opper vlak. Als een query reeks termen uit hetzelfde cluster bevat, wordt er als gevolg hiervan een document met beide een hogere rang orde dan een.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vector weergave voor context" border="true":::

## <a name="next-steps"></a>Volgende stappen

Semantische classificatie wordt aangeboden voor de standaard lagen in bepaalde regio's. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie en om u aan te melden.

Met een nieuw query type worden de rang schikking van de relevantie en de reactie structuren van semantisch zoeken ingeschakeld. [Maak een semantische query](semantic-how-to-query-request.md) om aan de slag te gaan.

U kunt ook een van de volgende artikelen door nemen voor gerelateerde informatie.

+ [Spelling controle toevoegen aan query termen](speller-how-to-add.md)
+ [Een semantisch antwoord retour neren](semantic-answers.md)