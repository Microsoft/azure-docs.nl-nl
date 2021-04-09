---
title: Verwijzing naar logische operator van OData
titleSuffix: Azure Cognitive Search
description: Syntaxis en referentie documentatie voor het gebruik van logische OData-Opera Tors, en, of en niet in azure Cognitive Search query's.
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
ms.openlocfilehash: 27d5427d34de591f9cfeab2310d79a2fde217624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88917870"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Logische OData-Opera tors in azure Cognitive Search- `and` , `or` , `not`

[OData-filter expressies](query-odata-filter-orderby-syntax.md) in azure Cognitive Search zijn Booleaanse expressies die de waarde van `true` of bepalen `false` . U kunt een complex filter schrijven door een reeks [eenvoudiger filters](search-query-odata-comparison-operators.md) te schrijven en deze samen te stellen met behulp van de logische Opera tors van [Boole algebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Een binaire operator waarmee wordt geëvalueerd `true` of de linker-en rechter-sub-expressies worden geëvalueerd `true` .
- `or`: Een binaire operator die evalueert of een `true` van de linker-of rechter-sub-expressies resulteert in `true` .
- `not`: Een unaire operator die evalueert `true` of de sub-expressie resulteert in `false` , en omgekeerd.

Met deze combi natie van de [verzamelings operators `any` en `all` ](search-query-odata-collection-operators.md)kunt u filters maken die zeer complexe zoek criteria kunnen uitdrukken.

## <a name="syntax"></a>Syntax

De volgende EBNF ([uitgebreid Backus-Naur formulier](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van een OData-expressie die gebruikmaakt van de logische Opera tors.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Er zijn twee soorten logische expressies: binary ( `and` / `or` ), waar er twee subexpressies en Unair ( `not` ) zijn, waarbij er slechts één wordt. De subexpressies kunnen Booleaanse expressies van elk soort zijn:

- Velden of bereik variabelen van het type `Edm.Boolean`
- Functies die waarden van het type retour neren `Edm.Boolean` , zoals `geo.intersects` of `search.ismatch`
- [Vergelijkings expressies](search-query-odata-comparison-operators.md), zoals `rating gt 4`
- [Verzamelings expressies](search-query-odata-collection-operators.md), zoals `Rooms/any(room: room/Type eq 'Deluxe Room')`
- De letterlijke Boole-waarden `true` of `false` .
- Andere logische expressies die zijn gemaakt met `and` , `or` en `not` .

> [!IMPORTANT]
> Er zijn enkele situaties waarin niet alle soorten Subexpressies kunnen worden gebruikt met, met `and` / `or` name binnen lambda-expressies. Zie [OData-verzamelings operatoren in Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) voor meer informatie.

### <a name="logical-operators-and-null"></a>Logische Opera tors en `null`

De meeste Booleaanse expressies zoals functies en vergelijkingen kunnen geen `null` waarden produceren en de logische Opera tors kunnen niet rechtstreeks op de `null` letterlijke waarde worden toegepast (bijvoorbeeld `x and null` is niet toegestaan). Boole-velden kunnen echter wel zijn `null` , dus u moet weten hoe de `and` `or` `not` Opera Tors, en zich gedragen in de aanwezigheid van Null. Dit wordt in de volgende tabel samenvatten, waarbij `b` een veld van het type is `Edm.Boolean` :

| Expression | Resultaat wanneer `b` is `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Wanneer een Boole-veld `b` in een filter expressie wordt weer gegeven, wordt gereageerd alsof het is geschreven `b eq true` . als dat `b` het geval is `null` , wordt de expressie geëvalueerd `false` . Dit werkt op dezelfde manier `not b` als `not (b eq true)` , zodat deze kan worden geëvalueerd `true` . Op deze manier `null` werken velden hetzelfde als `false` . Dit is consistent met de manier waarop ze zich gedragen wanneer ze worden gecombineerd met andere expressies die gebruikmaken van `and` en `or` , zoals wordt weer gegeven in de bovenstaande tabel. Ondanks dit geldt dat er nog steeds een directe vergelijking naar `false` ( `b eq false` ) wordt geëvalueerd `false` . Met andere woorden, `null` is niet gelijk aan `false` , hoewel het zich gedraagt als in Boole-expressies.

## <a name="examples"></a>Voorbeelden

Overeenkomende documenten waarbij het `rating` veld tussen 3 en 5 ligt:

```odata-filter-expr
    rating ge 3 and rating le 5
```

Overeenkomende documenten waarbij alle elementen van het `ratings` veld kleiner zijn dan 3 of groter dan 5:

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

Vergelijkt documenten waarbij het `location` veld binnen de gegeven veelhoek ligt en het document niet de term ' openbaar ' bevat.

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

Documenten voor hotels zoeken in Vancouver, Canada waar zich een luxe ruimte bevindt met een basis tempo van minder dan 160:

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)