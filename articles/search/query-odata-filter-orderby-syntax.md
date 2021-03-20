---
title: Overzicht van OData-taal
titleSuffix: Azure Cognitive Search
description: Overzicht van OData-taal voor filters, Select en order-by voor Azure Cognitive Search query's.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: d04311fce81d147a0830918aee1d4a2a9c0808d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88923395"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Overzicht van OData-taal voor `$filter` , `$orderby` en `$select` in azure Cognitive Search

Azure Cognitive Search ondersteunt een subset van de syntaxis van de OData-expressie voor **$filter**, **$OrderBy** en **$Select** expressies. Filter expressies worden geëvalueerd tijdens het parseren van query's, het beperken van zoek opdrachten naar specifieke velden of het toevoegen van match criteria die worden gebruikt tijdens index scans. Order-by-expressies worden toegepast als een verwerkings stap van een resultaatset om de geretourneerde documenten te sorteren. Expressies selecteren bepalen welke document velden worden opgenomen in de resultatenset. De syntaxis van deze expressies verschilt van de [eenvoudige](query-simple-syntax.md) of [volledige](query-lucene-syntax.md) query syntaxis die wordt gebruikt in de **Zoek** parameter, hoewel er wel een overlap is in de syntaxis voor verwijzende velden.

Dit artikel bevat een overzicht van de taal van de OData-expressie die wordt gebruikt in filters, order-by en expressies selecteren. De taal wordt ' bottom-up ' weer gegeven, te beginnen met de meest elementaire elementen en op basis hiervan te bouwen. De syntaxis op het hoogste niveau voor elke para meter wordt beschreven in een afzonderlijk artikel:

- [$filter syntaxis](search-query-odata-filter.md)
- [$orderby syntaxis](search-query-odata-orderby.md)
- [$select syntaxis](search-query-odata-select.md)

OData-expressies variëren van eenvoudig tot zeer complex, maar ze hebben allemaal algemene elementen. De meest elementaire onderdelen van een OData-expressie in azure Cognitive Search zijn:

- **Veld paden**, die verwijzen naar specifieke velden van uw index.
- **Constanten** zijn letterlijke waarden van een bepaald gegevens type.

> [!NOTE]
> De terminologie in azure Cognitive Search wijkt op een paar manieren af van de [OData-standaard](https://www.odata.org/documentation/) . Het aanroepen van een **veld** in azure Cognitive Search wordt een **eigenschap** genoemd in OData en op dezelfde manier als het **pad naar** een **eigenschap**. Een **index** met **documenten** in azure Cognitive Search wordt in OData meer algemeen aangeduid als een **entiteitset** met **entiteiten**. De terminologie van Azure Cognitive Search wordt overal in deze referentie gebruikt.

## <a name="field-paths"></a>Veld paden

Met de volgende EBNF ([uitgebreid Backus-Naur formulier](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) wordt de grammatica van veld paden gedefinieerd.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Een pad naar een veld bestaat uit een of meer **id's** , gescheiden door slashes. Elke id is een reeks tekens die moet beginnen met een ASCII-letter of onderstrepings teken en alleen ASCII-letters, cijfers of onderstrepings tekens bevatten. De letters kunnen in het hoofd of kleine letters worden getypt.

Een id kan verwijzen naar de naam van een veld of naar een **bereik variabele** in de context van een [verzamelings expressie](search-query-odata-collection-operators.md) ( `any` of `all` ) in een filter. Een bereik variabele is een lus-variabele die het huidige element van de verzameling weergeeft. Voor complexe verzamelingen vertegenwoordigt die variabele een object. dat is de reden waarom u veld paden kunt gebruiken om te verwijzen naar subvelden van de variabele. Dit is vergelijkbaar met punt notatie in veel programmeer talen.

Voor beelden van veld paden worden weer gegeven in de volgende tabel:

| Pad naar veld | Beschrijving |
| --- | --- |
| `HotelName` | Verwijst naar een veld op het hoogste niveau van de index |
| `Address/City` | Verwijst naar het `City` subveld van een complex veld in de index; `Address` is van het type `Edm.ComplexType` in dit voor beeld |
| `Rooms/Type` | Verwijst naar het `Type` subveld van een complex verzamelings veld in de index; `Rooms` is van het type `Collection(Edm.ComplexType)` in dit voor beeld |
| `Stores/Address/Country` | Verwijst naar het `Country` subveld van het `Address` subveld van een complex verzamelings veld in de index; `Stores` is van het type `Collection(Edm.ComplexType)` en `Address` is van het type `Edm.ComplexType` in dit voor beeld |
| `room/Type` | Verwijst naar het `Type` subveld van de `room` variabele Range, bijvoorbeeld in de filter expressie `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Verwijst naar het `Country` subveld van het `Address` subveld van de `store` bereik variabele, bijvoorbeeld in de filter expressie `Stores/any(store: store/Address/Country eq 'Canada')` |

De betekenis van een pad naar een veld verschilt, afhankelijk van de context. In filters verwijst een veld pad naar de waarde van *één exemplaar* van een veld in het huidige document. In andere contexten, zoals **$OrderBy**, **$Select** of in een [Zoek opdracht in de volledige lucene-syntaxis](query-lucene-syntax.md#bkmk_fields)verwijst een pad naar het veld zelf. Dit verschil heeft een aantal gevolgen voor de manier waarop u veld paden in filters gebruikt.

Overweeg het pad naar het veld `Address/City` . In een filter verwijst dit naar één plaats voor het huidige document, zoals ' San Francisco '. Verwijst daarentegen `Rooms/Type` naar het `Type` subveld voor veel kamers (zoals ' standaard ' voor de eerste kamer ' Deluxe ' voor de tweede kamer, enzovoort). Omdat `Rooms/Type` niet naar *één exemplaar* van het subveld verwijst `Type` , kan het niet rechtstreeks worden gebruikt in een filter. In plaats daarvan gebruikt u een [lambda-expressie](search-query-odata-collection-operators.md) met een bereik variabele, als u wilt filteren op room-type:

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

In dit voor beeld wordt de variabele Range `room` weer gegeven in het `room/Type` veld pad. Op die manier `room/Type` verwijst naar het type van de huidige ruimte in het huidige document. Dit is één exemplaar van het `Type` subveld, zodat het rechtstreeks in het filter kan worden gebruikt.

### <a name="using-field-paths"></a>Veld paden gebruiken

Veld paden worden gebruikt in veel para meters van de [Azure COGNITIVE Search rest-api's](/rest/api/searchservice/). De volgende tabel bevat alle locaties waar ze kunnen worden gebruikt, plus eventuele beperkingen op het gebruik ervan:

| API | Parameternaam | Beperkingen |
| --- | --- | --- |
| Index [maken](/rest/api/searchservice/create-index) of [bijwerken](/rest/api/searchservice/update-index) | `suggesters/sourceFields` | Geen |
| Index [maken](/rest/api/searchservice/create-index) of [bijwerken](/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | Kan alleen verwijzen naar **Doorzoek** bare velden |
| Index [maken](/rest/api/searchservice/create-index) of [bijwerken](/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | Kan alleen verwijzen naar **filter** bare velden |
| [Zoeken](/rest/api/searchservice/search-documents) | `search` Wanneer `queryType` is `full` | Kan alleen verwijzen naar **Doorzoek** bare velden |
| [Zoeken](/rest/api/searchservice/search-documents) | `facet` | Kan alleen verwijzen naar **facetbare** velden |
| [Zoeken](/rest/api/searchservice/search-documents) | `highlight` | Kan alleen verwijzen naar **Doorzoek** bare velden |
| [Zoeken](/rest/api/searchservice/search-documents) | `searchFields` | Kan alleen verwijzen naar **Doorzoek** bare velden |
| [Suggesties](/rest/api/searchservice/suggestions) en [automatisch aanvullen](/rest/api/searchservice/autocomplete) | `searchFields` | Kan alleen verwijzen naar velden die deel uitmaken van een [suggestie](index-add-suggesters.md) |
| [Zoeken](/rest/api/searchservice/search-documents), Voorst [Ellen](/rest/api/searchservice/suggestions)en [automatisch aanvullen](/rest/api/searchservice/autocomplete) | `$filter` | Kan alleen verwijzen naar **filter** bare velden |
| [Zoeken](/rest/api/searchservice/search-documents) en Voorst [Ellen](/rest/api/searchservice/suggestions) | `$orderby` | Kan alleen verwijzen naar **Sorteer** bare velden |
| [Zoeken](/rest/api/searchservice/search-documents), Voorst [Ellen](/rest/api/searchservice/suggestions)en [Opzoeken](/rest/api/searchservice/lookup-document) | `$select` | Kan alleen verwijzen naar **ophaalbaar** velden |

## <a name="constants"></a>Constanten

Constanten in OData zijn letterlijke waarden van een bepaald type [Entity Data Model](/dotnet/framework/data/adonet/entity-data-model) (EDM). Zie [ondersteunde gegevens typen](/rest/api/searchservice/supported-data-types) voor een lijst met ondersteunde typen in azure Cognitive Search. Constanten van verzamelings typen worden niet ondersteund.

De volgende tabel bevat voor beelden van constanten voor elk van de gegevens typen die worden ondersteund door Azure Cognitive Search:

| Gegevenstype | Voorbeeld constanten |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Speciale tekens in teken reeks constanten

Teken reeks constanten in OData worden gescheiden door enkele aanhalings tekens. Als u een query wilt maken met een teken reeks constante die mogelijk enkele aanhalings tekens bevat, kunt u de Inge sloten aanhalings tekens sluiten door ze te verdubbelen.

Een woord groep met een niet-geformatteerde apostrof zoals "Car auto" wordt bijvoorbeeld weer gegeven in OData als de teken reeks constante `'Alice''s car'` .

> [!IMPORTANT]
> Wanneer u via een programma filters maakt, is het belang rijk om teken reeks constanten te onthouden die afkomstig zijn van gebruikers invoer. Zo kunt u de kans op [ininjectie aanvallen](https://wikipedia.org/wiki/SQL_injection)beperken, met name bij het gebruik van filters voor het implementeren van [beveiligings beperking](search-security-trimming-for-azure-search.md).

### <a name="constants-syntax"></a>Syntaxis van constanten

De volgende EBNF ([uitgebreid Backus-Naur formulier](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica voor de meeste constanten die in de bovenstaande tabel worden weer gegeven. De grammatica voor geo-ruimtelijke typen vindt u in de [georuimtelijke functies van OData in Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

## <a name="building-expressions-from-field-paths-and-constants"></a>Expressies maken op basis van veld paden en constanten

Veld paden en constanten vormen het meest eenvoudige deel van een OData-expressie, maar ze zijn al volledige expressies zelf. In feite is de **$Select** para meter in azure Cognitive Search niets, maar een door komma's gescheiden lijst met veld paden en **$OrderBy** niet veel ingewik kelder dan **$Select**. Als u een veld van `Edm.Boolean` het type in de index wilt hebben, kunt u zelfs een filter schrijven dat niets maar het pad van dat veld is. De constanten `true` en `false` zijn ook geldige filters.

In de meeste gevallen hebt u echter complexere expressies nodig die naar meer dan een veld en constante verwijzen. Deze expressies zijn op verschillende manieren gebouwd, afhankelijk van de para meter.

In de volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) wordt de grammatica voor de para meters **$filter**, **$OrderBy** en **$Select** gedefinieerd. Deze zijn gebaseerd op eenvoudige expressies die verwijzen naar veld paden en constanten:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

De para meters **$OrderBy** en **$Select** zijn zowel door komma's gescheiden lijsten met eenvoudigere expressies. De para meter **$filter** is een booleaanse expressie die bestaat uit een eenvoudiger subexpressie. Deze subexpressies worden gecombineerd met behulp van logische Opera tors zoals [ `and` ,, `or` en `not` ](search-query-odata-logical-operators.md), vergelijkings operatoren zoals [ `eq` ,, `lt` `gt` ,](search-query-odata-comparison-operators.md)enzovoort, en verzamelings operators, zoals [ `any` en `all` ](search-query-odata-collection-operators.md).

De para meters **$filter**, **$OrderBy** en **$Select** worden uitgebreid beschreven in de volgende artikelen:

- [OData-$filter syntaxis in azure Cognitive Search](search-query-odata-filter.md)
- [OData-$orderby syntaxis in azure Cognitive Search](search-query-odata-orderby.md)
- [OData-$select syntaxis in azure Cognitive Search](search-query-odata-select.md)

## <a name="see-also"></a>Zie ook  

- [Facet navigatie in azure Cognitive Search](search-faceted-navigation.md)
- [Filters in azure Cognitive Search](search-filters.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene-query syntaxis](query-lucene-syntax.md)
- [Eenvoudige query syntaxis in azure Cognitive Search](query-simple-syntax.md)