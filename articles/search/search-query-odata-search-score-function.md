---
title: Naslag informatie over de functie OData Search. Score
titleSuffix: Azure Cognitive Search
description: Syntaxis en referentie documentatie voor het gebruik van de functie Search. Score in azure Cognitive Search query's.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0a84d0310573a1210e21157102a445fff9244782
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88923973"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData- `search.score` functie in Azure Cognitive Search

Wanneer u een query verzendt naar Azure Cognitive Search zonder de [para meter **$OrderBy**](search-query-odata-orderby.md), worden de resultaten die terugkomen in aflopende volg orde gesorteerd op relevantie score. Zelfs wanneer u **$OrderBy** gebruikt, wordt de relevantie score standaard gebruikt om bindingen te verstoren. Soms is het echter handig om de relevantie score te gebruiken als eerste Sorteer criterium en enkele andere criteria als de afbreeker. `search.score`Met de functie kunt u dit doen.

## <a name="syntax"></a>Syntax

De syntaxis voor `search.score` in **$OrderBy** is `search.score()` . De functie `search.score` voert geen para meters uit. Deze kan worden gebruikt in combi natie met de `asc` specificatie of de `desc` sorteer volgorde, net als bij elke andere component in de para meter **$OrderBy** . Dit kan ergens in de lijst met Sorteer criteria worden weer gegeven.

## <a name="example"></a>Voorbeeld

Sorteer hotels in aflopende volg orde op `search.score` en `rating` , en vervolgens in oplopende volg orde op afstand van de opgegeven coördinaten, zodat tussen twee hotels met een identieke classificatie, het dichtstbijzijnde item bovenaan wordt weer gegeven:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Volgende stappen  

- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search EST API&#41;](/rest/api/searchservice/Search-Documents)