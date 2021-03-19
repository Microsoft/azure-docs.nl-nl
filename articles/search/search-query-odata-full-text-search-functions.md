---
title: Naslag informatie voor zoeken in volledige tekst van OData
titleSuffix: Azure Cognitive Search
description: OData-functies voor zoeken in volledige tekst, Search. ismatch en Search. ismatchscoring, in azure Cognitive Search query's.
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
ms.openlocfilehash: 78f9e4d8fa80fdf74bdb5cd79f4489d12696fcc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935786"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData-functies voor zoeken in volledige tekst in azure Cognitive Search- `search.ismatch` en `search.ismatchscoring`

Azure Cognitive Search ondersteunt zoeken in volledige tekst in de context van [OData-filter expressies](query-odata-filter-orderby-syntax.md) via `search.ismatch` de `search.ismatchscoring` functies en. Met deze functies kunt u Zoek opdrachten in volledige tekst combi neren met strikte Booleaanse filtering op manieren die niet mogelijk zijn alleen met behulp van de para meter op het hoogste niveau `search` van de [zoek-API](/rest/api/searchservice/search-documents).

> [!NOTE]
> De `search.ismatch` `search.ismatchscoring` functies en worden alleen ondersteund in filters in de [zoek-API](/rest/api/searchservice/search-documents). Ze worden niet ondersteund in de api's [suggesties](/rest/api/searchservice/suggestions) of [automatisch aanvullen](/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Syntax

De volgende EBNF ([uitgebreid Backus-Naur formulier](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van de `search.ismatch` `search.ismatchscoring` functies en:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

### <a name="searchismatch"></a>Search. ismatch

De `search.ismatch` functie evalueert een zoek opdracht in volledige tekst als onderdeel van een filter expressie. De documenten die overeenkomen met de zoek query worden geretourneerd in de resultatenset. De volgende Overloads van deze functie zijn beschikbaar:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

De para meters worden gedefinieerd in de volgende tabel:

| Parameternaam | Type | Beschrijving |
| --- | --- | --- |
| `search` | `Edm.String` | De zoek query (in een [eenvoudige](query-simple-syntax.md) of [volledige](query-lucene-syntax.md) lucene-query syntaxis). |
| `searchFields` | `Edm.String` | Een door komma's gescheiden lijst met Doorzoek bare velden waarnaar moet worden gezocht; wordt standaard ingesteld op alle Doorzoek bare velden in de index. Bij gebruik van een [Zoek opdracht](query-lucene-syntax.md#bkmk_fields) in de `search` para meter, overschrijven de veld aanduidingen in de Lucene-query alle velden die zijn opgegeven in deze para meter. |
| `queryType` | `Edm.String` | `'simple'` of `'full'` ; standaard ingesteld op `'simple'` . Hiermee geeft u op welke query taal in de `search` para meter is gebruikt. |
| `searchMode` | `Edm.String` | `'any'` of `'all'` is standaard ingesteld op `'any'` . Hiermee wordt aangegeven of een of meer zoek termen in de `search` para meter moeten worden afgestemd om het document als een overeenkomst te tellen. Wanneer u de [lucene Booleaanse Opera tors](query-lucene-syntax.md#bkmk_boolean) in de `search` para meter gebruikt, hebben ze prioriteit boven deze para meter. |

Alle bovenstaande para meters zijn gelijk aan de overeenkomstige [para meters voor zoek aanvragen in de zoek-API](/rest/api/searchservice/search-documents).

De `search.ismatch` functie retourneert een waarde van het type `Edm.Boolean` , zodat u deze kunt samen stellen met andere filter subexpressies met behulp van de [logische booleaanse Opera tors](search-query-odata-logical-operators.md).

> [!NOTE]
> Azure Cognitive Search biedt geen ondersteuning voor het gebruik van `search.ismatch` of `search.ismatchscoring` binnen lambda-expressies. Dit betekent dat het niet mogelijk is om filters te schrijven over verzamelingen objecten die kunnen correleren met Zoek opdrachten in volledige tekst met strikte filter overeenkomsten voor hetzelfde object. Zie [problemen met verzamelings filters in Azure Cognitive Search oplossen](search-query-troubleshoot-collection-filters.md)voor meer informatie over deze beperking en voor beelden. Zie voor meer informatie over de reden voor het bestaan van de begrensde [verzamelings filters in Azure Cognitive Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Search. ismatchscoring

De `search.ismatchscoring` functie, zoals de `search.ismatch` functie, retourneert `true` voor documenten die overeenkomen met de query voor zoeken in volledige tekst, door gegeven als een para meter. Het verschil ertussen is dat de relevantie Score van documenten die overeenkomen met de `search.ismatchscoring` query, bijdraagt aan de totale document Score, in het geval van `search.ismatch` , de document score niet wordt gewijzigd. De volgende overbelastingen van deze functie zijn beschikbaar met para meters die identiek zijn aan die van `search.ismatch` :

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Zowel de `search.ismatch` als- `search.ismatchscoring` functies kunnen in dezelfde filter expressie worden gebruikt.

## <a name="examples"></a>Voorbeelden

Vind documenten met het woord ' afgebakend '. Deze filter query is identiek aan een [Zoek opdracht](/rest/api/searchservice/search-documents) met `search=waterfront` .

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

Vind documenten met het woord ' Hostel ' en classificatie groter of gelijk aan 4, of documenten met het woord ' Motel ' en de classificatie is gelijk aan 5. Opmerking: deze aanvraag kan niet worden aangegeven zonder de `search.ismatchscoring` functie.

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

Documenten zonder het woord "luxe" zoeken.

```odata-filter-expr
    not search.ismatch('luxury')
```

Documenten zoeken met de woord weer gave ' Oceaan ' of de classificatie gelijk aan 5. De `search.ismatchscoring` query wordt alleen uitgevoerd op velden `HotelName` en `Rooms/Description` .

Documenten die overeenkomen met alleen de tweede component van de schei ding, retour neren te veel-hotels met een waarde die `Rating` gelijk is aan 5. Om het duidelijk te maken dat deze documenten niet overeenkomen met een van de gescoorde delen van de expressie, worden deze geretourneerd met een score die gelijk is aan nul.

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

Vind documenten waar de termen "Hotel" en "lucht haven" binnen vijf woorden van elkaar zijn genoteerd in de beschrijving van het hotel en wanneer roken niet in ten minste een deel van de kamers is toegestaan. Deze query maakt gebruik van de [volledige lucene-query taal](query-lucene-syntax.md).

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)