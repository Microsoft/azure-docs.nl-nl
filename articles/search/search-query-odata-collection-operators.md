---
title: Verwijzing naar OData-verzamelings operator
titleSuffix: Azure Cognitive Search
description: Bij het maken van filter expressies in azure Cognitive Search query's gebruikt u ' any ' en ' alle ' Opera tors in lambda-expressies wanneer het filter zich op een verzameling of een complexe verzamelings veld bevindt.
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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934902"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>OData-verzamelings operators in azure Cognitive Search- `any` en `all`

Wanneer u een [OData-filter expressie](query-odata-filter-orderby-syntax.md) schrijft om te gebruiken met Azure Cognitive Search, is het vaak handig om te filteren op verzamelings velden. U kunt dit doen met behulp van de `any` `all` Opera tors en.

## <a name="syntax"></a>Syntax

De volgende EBNF ([uitgebreid Backus-Naur formulier](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van een OData-expressie die gebruikmaakt van `any` of `all` .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Er zijn drie soorten expressies waarmee verzamelingen worden gefilterd.

- De eerste twee herhaalde iteratie van een verzamelings veld, waarbij een predikaat wordt toegepast dat is opgegeven in de vorm van een lambda-expressie voor elk element van de verzameling.
  - Een expressie die gebruikmaakt van `all` retourneert `true` als het predicaat True is voor elk element van de verzameling.
  - Een expressie die gebruikmaakt van `any` retourneert `true` als het predicaat True is voor ten minste één element van de verzameling.
- Het derde formulier van het verzamelings filter gebruikt `any` zonder een lambda-expressie om te testen of een veld in de verzameling leeg is. Als de verzameling elementen bevat, wordt deze geretourneerd `true` . Als de verzameling leeg is, wordt geretourneerd `false` .

Een **lambda-expressie** in een verzamelings filter is net als de hoofd tekst van een lus in een programmeer taal. Hiermee wordt een variabele gedefinieerd, de **variabele Range** genoemd, die het huidige element van de verzameling tijdens iteratie bevat. Er wordt ook een andere booleaanse expressie gedefinieerd die de filter criteria is die moeten worden toegepast op de bereik variabele voor elk element van de verzameling.

## <a name="examples"></a>Voorbeelden

Overeenkomende documenten waarvan `tags` het veld precies de teken reeks ' WiFi ' bevat:

```text
tags/any(t: t eq 'wifi')
```

Vergelijkt documenten waarbij elk element van het `ratings` veld tussen 3 en 5 ligt:

```text
ratings/all(r: r ge 3 and r le 5)
```

Vergelijkt documenten waarbij een van de geo-coördinaten in het `locations` veld binnen de opgegeven veelhoek ligt:

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

Overeenkomende documenten waarbij het `rooms` veld leeg is:

```text
not rooms/any()
```

Documenten die voor alle ruimten overeenkomen, het `rooms/amenities` veld bevat ' TV ' en `rooms/baseRate` is kleiner dan 100:

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>Beperkingen

Niet elke functie van filter expressies is beschikbaar in de hoofd tekst van een lambda-expressie. De beperkingen variëren, afhankelijk van het gegevens type van het verzamelings veld dat u wilt filteren. De volgende tabel geeft een overzicht van de beperkingen.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Zie [problemen met verzamelings filters in Azure Cognitive Search oplossen](search-query-troubleshoot-collection-filters.md)voor meer informatie over deze beperkingen en voor beelden. Zie informatie over het [verzamelen van filters in Azure Cognitive Search](search-query-understand-collection-filters.md)voor meer gedetailleerde informatie over de oorzaak van deze beperkingen.

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)