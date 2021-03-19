---
title: Verwijzing naar OData-vergelijkings operator
titleSuffix: Azure Cognitive Search
description: Syntaxis en referentie documentatie voor het gebruik van OData-vergelijkings operatoren (EQ, ne, gt, lt, ge en Le) in azure Cognitive Search query's.
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
ms.openlocfilehash: fc5803f96c30ea1df362676aa8c4104bb0b69db3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934868"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData-vergelijkings operatoren in azure Cognitive Search-,,,, en `eq` `ne` `gt` `lt` `ge``le`

De meest eenvoudige bewerking in een [OData-filter expressie](query-odata-filter-orderby-syntax.md) in azure Cognitive Search is het vergelijken van een veld met een bepaalde waarde. Er zijn twee soorten vergelijking mogelijk: gelijkheids vergelijking en bereik vergelijking. U kunt de volgende operators gebruiken om een veld te vergelijken met een constante waarde:

Gelijkheidsoperators:

- `eq`: Test of een veld **gelijk is aan** een constante waarde
- `ne`: Testen of een veld **niet gelijk is aan** een constante waarde

Bereikoperators:

- `gt`: Testen of een veld **groter is dan** een constante waarde
- `lt`: Testen of een veld **kleiner is dan** een constante waarde
- `ge`: Test of een veld **groter is dan of gelijk is aan** een constante waarde
- `le`: Testen of een veld **kleiner is dan of gelijk is aan** een constante waarde

U kunt de bereik operatoren in combi natie met de [logische Opera tors](search-query-odata-logical-operators.md) gebruiken om te testen of een veld binnen een bepaald waardebereik ligt. Zie de [voor beelden](#examples) verderop in dit artikel.

> [!NOTE]
> Als u wilt, kunt u de constante waarde aan de linkerkant van de operator en de veld naam aan de rechter kant plaatsen. Voor bereik Opera tors wordt de betekenis van de vergelijking omgekeerd. Als de constante waarde bijvoorbeeld aan de linkerkant is, wordt `gt` getest of de constante waarde groter is dan het veld. U kunt ook de vergelijkings operators gebruiken om het resultaat van een functie, zoals `geo.distance` , met een waarde te vergelijken. Voor Booleaanse functies zoals `search.ismatch` , het vergelijken van het resultaat naar `true` of `false` is optioneel.

## <a name="syntax"></a>Syntax

De volgende EBNF ([uitgebreid Backus-Naur formulier](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van een OData-expressie die gebruikmaakt van de vergelijkings operators.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Er zijn twee vormen van vergelijkings expressies. Het enige verschil tussen deze items is of de constante wordt weer gegeven aan de linkerkant of de rechter kant van de operator. De expressie aan de andere kant van de operator moet een **variabele** of een functie aanroep zijn. Een variabele kan een veld naam of een bereik variabele zijn in het geval van een lambda- [expressie](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Gegevens typen voor vergelijkingen

De gegevens typen aan beide zijden van een vergelijkings operator moeten compatibel zijn. Als de linkerkant bijvoorbeeld een veld van het type is `Edm.DateTimeOffset` , moet de rechter kant een datum-tijd-constante zijn. Numerieke gegevens typen zijn flexibeler. U kunt variabelen en functies van elk numeriek type vergelijken met constanten van elk ander numeriek type, met enkele beperkingen, zoals beschreven in de volgende tabel.

| Variabele of functie type | Type constante waarde | Beperkingen |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | De vergelijking is onderhevig aan [speciale `NaN` regels voor](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Constante wordt geconverteerd naar `Edm.Double` , wat resulteert in een precisie verlies voor waarden van grote grootte |
| `Edm.Double` | `Edm.Int32` | n.v.t. |
| `Edm.Int64` | `Edm.Double` | Vergelijkingen naar `NaN` , `-INF` of `INF` zijn niet toegestaan |
| `Edm.Int64` | `Edm.Int64` | n.v.t. |
| `Edm.Int64` | `Edm.Int32` | Constante wordt geconverteerd naar `Edm.Int64` vóór vergelijking |
| `Edm.Int32` | `Edm.Double` | Vergelijkingen naar `NaN` , `-INF` of `INF` zijn niet toegestaan |
| `Edm.Int32` | `Edm.Int64` | n.v.t. |
| `Edm.Int32` | `Edm.Int32` | n.v.t. |

Voor vergelijkingen die niet zijn toegestaan, zoals het vergelijken van een veld van `Edm.Int64` het type naar `NaN` , retourneert de Azure Cognitive Search rest API de fout ' http 400: onjuiste aanvraag '.

> [!IMPORTANT]
> Hoewel numerieke type vergelijkingen flexibel zijn, raden we u aan om vergelijkingen in filters te schrijven, zodat de constante waarde van hetzelfde gegevens type is als de variabele of functie waarmee deze wordt vergeleken. Dit is vooral belang rijk bij het combi neren van drijvende-komma-en integerwaarden, waarbij impliciete conversies die nauw keurigheid kunnen verliezen, mogelijk zijn.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Speciale gevallen voor `null` en `NaN`

Wanneer vergelijkings operatoren worden gebruikt, is het belang rijk te weten dat alle niet-verzamelings velden in azure Cognitive Search mogelijk zijn `null` . De volgende tabel bevat alle mogelijke resultaten voor een vergelijkings expressie waarbij beide zijden kunnen zijn `null` :

| Operator | Resultaat wanneer alleen het veld of de variabele is `null` | Resultaat wanneer alleen de constante is `null` | Resultaat wanneer het veld of de variabele en de constante worden `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: fout met ongeldige aanvraag | HTTP 400: fout met ongeldige aanvraag |
| `lt` | `false` | HTTP 400: fout met ongeldige aanvraag | HTTP 400: fout met ongeldige aanvraag |
| `ge` | `false` | HTTP 400: fout met ongeldige aanvraag | HTTP 400: fout met ongeldige aanvraag |
| `le` | `false` | HTTP 400: fout met ongeldige aanvraag | HTTP 400: fout met ongeldige aanvraag |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

In samen vatting `null` is alleen gelijk aan zichzelf en is niet kleiner dan of groter dan een andere waarde.

Als uw index velden van het type bevat `Edm.Double` en u `NaN` waarden naar die velden uploadt, moet u er rekening mee doen wanneer u filters schrijft. Azure Cognitive Search implementeert de IEEE 754-standaard voor afhandelings `NaN` waarden en vergelijkingen met een dergelijke waarde geven geen duidelijke resultaten, zoals wordt weer gegeven in de volgende tabel.

| Operator | Resultaat wanneer ten minste één operand is `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Samen vatting `NaN` is niet gelijk aan een wille keurige waarde, met inbegrip van zichzelf.

### <a name="comparing-geo-spatial-data"></a>Geografische ruimtelijke gegevens vergelijken

Het is niet mogelijk om een veld van het type rechtstreeks `Edm.GeographyPoint` te vergelijken met een constante waarde, maar u kunt de `geo.distance` functie gebruiken. Deze functie retourneert een waarde van het type `Edm.Double` , zodat u deze kunt vergelijken met een numerieke constante die moet worden gefilterd op basis van de afstand van constante geo-ruimtelijke coördinaten. Zie de onderstaande [voor beelden](#examples) .

### <a name="comparing-string-data"></a>Teken reeks gegevens vergelijken

Teken reeksen kunnen worden vergeleken in filters voor exacte overeenkomsten met behulp van de `eq` `ne` Opera tors. Deze vergelijkingen zijn hoofdletter gevoelig.

## <a name="examples"></a>Voorbeelden

Overeenkomende documenten waarbij het `Rating` veld tussen 3 en 5 ligt:

```text
Rating ge 3 and Rating le 5
```

Documenten vergelijken waarbij het `Location` veld kleiner is dan 2 kilo meters van de opgegeven breedte graad en lengte graad:

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

Documenten vergelijken waarbij het `LastRenovationDate` veld groter is dan of gelijk is aan 1 januari, 2015, Midnight UTC:

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

Documenten die overeenkomen met het `Details/Sku` veld `null` :

```text
Details/Sku ne null
```

Vergelijkings documenten voor hotels waarbij ten minste één kamer het type ' Deluxe room ' heeft, waarbij de teken reeks van het `Rooms/Type` veld precies overeenkomt met het filter:

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)