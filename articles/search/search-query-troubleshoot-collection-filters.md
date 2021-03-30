---
title: Problemen met OData-verzamelings filters oplossen
titleSuffix: Azure Cognitive Search
description: Leer benaderingen voor het oplossen van filter fouten van OData-verzamelingen in azure Cognitive Search query's.
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
ms.openlocfilehash: 3050f701c11773207aa6054d4d08d908d87b2ce7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88932063"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Problemen met OData-verzamelings filters in azure Cognitive Search oplossen

Als u wilt [filteren](query-odata-filter-orderby-syntax.md) op verzamelings velden in azure Cognitive Search, kunt u de [ `any` `all` Opera tors en](search-query-odata-collection-operators.md) gebruiken in combi natie met **lambda-expressies**. Een lambda-expressie is een subfilter dat op elk element van een verzameling wordt toegepast.

Niet elke functie van filter expressies is beschikbaar in een lambda-expressie. Welke functies beschikbaar zijn, is afhankelijk van het gegevens type van het verzamelings veld dat u wilt filteren. Dit kan een fout veroorzaken als u probeert een functie te gebruiken in een lambda-expressie die niet wordt ondersteund in deze context. Als er dergelijke fouten optreden tijdens het schrijven van een complex filter voor verzamelings velden, helpt dit artikel bij het oplossen van het probleem.

## <a name="common-collection-filter-errors"></a>Veelvoorkomende fouten bij verzamelings filter

De volgende tabel bevat fouten die kunnen optreden wanneer u probeert een verzamelings filter uit te voeren. Deze fouten doen zich voor wanneer u een functie van filter expressies gebruikt die niet worden ondersteund binnen een lambda-expressie. Elke fout geeft een aantal richt lijnen over hoe u het filter opnieuw kunt schrijven om de fout te voor komen. De tabel bevat ook een koppeling naar de relevante sectie van dit artikel met meer informatie over hoe u deze fout kunt voor komen.

| Foutbericht | Hiervan | Zie voor meer informatie |
| --- | --- | --- |
| De functie ismatch heeft geen para meters die zijn gekoppeld aan de bereik variabele. Alleen gebonden veld verwijzingen worden ondersteund binnen lambda-expressies (' any ' en ' all '). Wijzig het filter zodanig dat de functie ismatch zich buiten de lambda-expressie bevindt en probeer het opnieuw. | Met `search.ismatch` of `search.ismatchscoring` binnen een lambda-expressie | [Regels voor het filteren van complexe verzamelingen](#bkmk_complex) |
| Ongeldige lambda-expressie. Er is een test gevonden voor gelijkheid of ongelijkheid, waarbij het tegenovergestelde werd verwacht in een lambda-expressie die een veld van het type verzameling (EDM. String) doorloopt. Gebruik voor ' any ' expressies van het formulier ' x EQ y ' of ' search.in (...) '. Gebruik voor ' all ' expressies van de vorm ' x-y ', ' niet (x-EQ y) ' of ' niet search.in (...) '. | Filteren op een veld van het type `Collection(Edm.String)` | [Regels voor het filteren van teken reeks verzamelingen](#bkmk_strings) |
| Ongeldige lambda-expressie. Er is een niet-ondersteunde vorm van een complexe Boole-expressie gevonden. Voor any, gebruikt u expressies die or zijn van and, ook wel bekend als disjunctive Normal form. Bijvoorbeeld: (a en b) of (c en d), waarbij a, b, c en d, worden vergeleken of gelijkheid van onderliggende expressies. Gebruik voor ' all ' expressies die ' and of or ' zijn, ook wel bekend als Conjunctive Normal form. Bijvoorbeeld: (a of b) en (c of d) waarbij a, b, c en d een vergelijking of ongelijke subexpressie zijn. Voor beelden van vergelijkings expressies: x gt 5, x Le 2. Voor beeld van een gelijkheids expressie: ' x EQ 5 '. Voor beeld van een expressie voor ongelijkheid: ' x ne 5 '. | Filteren op velden van het type `Collection(Edm.DateTimeOffset)` , `Collection(Edm.Double)` , `Collection(Edm.Int32)` of `Collection(Edm.Int64)` | [Regels voor het filteren van vergelijk bare verzamelingen](#bkmk_comparables) |
| Ongeldige lambda-expressie. Er is een niet-ondersteund gebruik van geo. distance () of geo. intersects () gevonden in een lambda-expressie die wordt herhaald voor een veld van het type verzameling (EDM. GeographyPoint). Voor any moet u geo. distance () vergelijken met de Opera tors lt of Le en ervoor zorgen dat het gebruik van geo. intersects () niet wordt genegeerd. Voor alle moet u geo. distance () vergelijken met de Opera tors gt of ge en ervoor zorgen dat elk gebruik van geo. intersects () wordt genegeerd. | Filteren op een veld van het type `Collection(Edm.GeographyPoint)` | [Regels voor het filteren van GeographyPoint-verzamelingen](#bkmk_geopoints) |
| Ongeldige lambda-expressie. Complexe Boole-expressies worden niet ondersteund in lambda-expressies die worden herhaald voor velden van het type verzameling (EDM. GeographyPoint). Voor any, moet u lid zijn van subexpressies met ' or '; ' en ' worden niet ondersteund. Voor ' all ' moet u deel nemen aan subexpressies met ' en '. ' or ' wordt niet ondersteund. | Filteren op velden van het type `Collection(Edm.String)` of `Collection(Edm.GeographyPoint)` | [Regels voor het filteren van teken reeks verzamelingen](#bkmk_strings) <br/><br/> [Regels voor het filteren van GeographyPoint-verzamelingen](#bkmk_geopoints) |
| Ongeldige lambda-expressie. Er is een vergelijkings operator gevonden (een van de ' lt ', ' Le ', ' gt ' of ' ge '). Alleen gelijkheids operatoren zijn toegestaan in lambda-expressies die worden herhaald voor velden van het type verzameling (EDM. String). Voor any, gebruikt u expressies van het formulier x EQ y. Gebruik voor ' all ' expressies van het formulier ' x-e-y ' of ' niet (x-EQ y) '. | Filteren op een veld van het type `Collection(Edm.String)` | [Regels voor het filteren van teken reeks verzamelingen](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Geldige verzamelings filters schrijven

De regels voor het schrijven van geldige verzamelings filters verschillen voor elk gegevens type. In de volgende secties worden de regels beschreven door voor beelden te bekijken van welke filter functies worden ondersteund en welke niet:

- [Regels voor het filteren van teken reeks verzamelingen](#bkmk_strings)
- [Regels voor het filteren van Booleaanse verzamelingen](#bkmk_bools)
- [Regels voor het filteren van GeographyPoint-verzamelingen](#bkmk_geopoints)
- [Regels voor het filteren van vergelijk bare verzamelingen](#bkmk_comparables)
- [Regels voor het filteren van complexe verzamelingen](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regels voor het filteren van teken reeks verzamelingen

In lambda-expressies voor teken reeks verzamelingen worden de enige vergelijkings operatoren die kunnen worden gebruikt, `eq` en `ne` .

> [!NOTE]
> Azure Cognitive Search biedt geen ondersteuning `lt` / `le` / `gt` / `ge` voor de Opera tors voor teken reeksen, zowel binnen als buiten een lambda-expressie.

De hoofd tekst van een `any` kan alleen testen op gelijkheid terwijl de hoofd tekst van een `all` alleen test op ongelijkheid kan worden getest.

Het is ook mogelijk om meerdere expressies te combi neren via `or` in de hoofd tekst van een `any` en via `and` in de hoofd tekst van een `all` . Aangezien de `search.in` functie gelijk is aan het combi neren van gelijkheids controles met, is het `or` ook toegestaan in de hoofd tekst van een `any` . Is daarentegen `not search.in` toegestaan in de hoofd tekst van een `all` .

Deze expressies zijn bijvoorbeeld toegestaan:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

Deze expressies zijn niet toegestaan:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regels voor het filteren van Booleaanse verzamelingen

Het type `Edm.Boolean` ondersteunt alleen de `eq` `ne` Opera tors en. Daarom is het niet veel zinvol om dergelijke componenten toe te voegen die dezelfde bereik variabele controleren, `and` / `or` omdat deze altijd zou leiden tot tautologies of tegen strijdigheden.

Hier volgen enkele voor beelden van filters voor Booleaanse verzamelingen die zijn toegestaan:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

In tegens telling tot teken reeks verzamelingen hebben Boole-verzamelingen geen limieten voor de operator die kan worden gebruikt in welk type lambda-expressie. Beide `eq` en `ne` kunnen worden gebruikt in de hoofd tekst van `any` of `all` .

Expressies zoals de volgende zijn niet toegestaan voor Booleaanse verzamelingen:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regels voor het filteren van GeographyPoint-verzamelingen

Waarden van `Edm.GeographyPoint` het type in een verzameling kunnen niet rechtstreeks met elkaar worden vergeleken. In plaats daarvan moeten ze worden gebruikt als para meters voor de `geo.distance` `geo.intersects` functies en. De `geo.distance` functie moet op zijn beurt worden vergeleken met een afstands waarde met behulp van een van de vergelijkings operatoren `lt` , `le` , `gt` of `ge` . Deze regels zijn ook van toepassing op velden van het soort EDM. GeographyPoint die niet zijn verzameld.

Net als teken reeks verzamelingen `Edm.GeographyPoint` hebben verzamelingen enkele regels voor de manier waarop de georuimtelijke functies kunnen worden gebruikt en gecombineerd in de verschillende soorten lambda-expressies:

- Welke vergelijkings operatoren u met de `geo.distance` functie kunt gebruiken, is afhankelijk van het type lambda-expressie. Voor `any` kunt u alleen of gebruiken `lt` `le` . Voor `all` kunt u alleen of gebruiken `gt` `ge` . U kunt expressies negeren `geo.distance` met, maar u moet de vergelijkings operator wijzigen ( `geo.distance(...) lt x` wordt in `not (geo.distance(...) ge x)` en `geo.distance(...) le x` wordt `not (geo.distance(...) gt x)` ).
- In de hoofd tekst van een `all` `geo.intersects` moet de functie worden genegeerd. De functie mag daarentegen in de hoofd tekst van een `any` `geo.intersects` niet worden genegeerd.
- In de hoofd tekst van een `any` kunnen geo-spatiale expressies worden gecombineerd met `or` . In de hoofd tekst van een `all` kunnen dergelijke expressies worden gecombineerd met `and` .

De bovenstaande beperkingen bestaan om Vergelijk bare redenen als de beperking voor gelijkheid/ongelijkheid voor teken reeks verzamelingen. Zie meer [informatie over OData-verzamelings filters in Azure Cognitive Search](search-query-understand-collection-filters.md) voor een diep gaande blik op deze redenen.

Hier volgen enkele voor beelden van filters voor `Edm.GeographyPoint` verzamelingen die zijn toegestaan:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Expressies zoals het volgende zijn niet toegestaan voor `Edm.GeographyPoint` verzamelingen:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regels voor het filteren van vergelijk bare verzamelingen

Deze sectie is van toepassing op alle volgende gegevens typen:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typen zoals `Edm.Int32` en `Edm.DateTimeOffset` ondersteunen alle zes van de vergelijkings operatoren: `eq` , `ne` ,,, `lt` `le` `gt` en `ge` . Lambda-expressies over verzamelingen van deze typen kunnen eenvoudige expressies met een van deze opera tors bevatten. Dit geldt voor zowel `any` als `all` . Deze filters zijn bijvoorbeeld toegestaan:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Er zijn echter beperkingen ten aanzien van de manier waarop dergelijke vergelijkings expressies kunnen worden gecombineerd tot complexere expressies binnen een lambda-expressie:

- Regels voor `any` :
  - Expressies met een eenvoudige ongelijkheid kunnen niet worden gebruikt in combi natie met andere expressies. Deze expressie is bijvoorbeeld toegestaan:
    - `ratings/any(r: r ne 5)`

    maar deze expressie is niet:
    - `ratings/any(r: r ne 5 and r gt 2)`

    en hoewel deze expressie is toegestaan, is het niet nuttig omdat de voor waarden elkaar overlappen:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Eenvoudige vergelijkings expressies waarbij,,, `eq` `lt` `le` `gt` of `ge` kunnen worden gecombineerd met `and` / `or` . Bijvoorbeeld:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Vergelijkings expressies gecombineerd met `and` (samen voegingen) kunnen verder worden gecombineerd met `or` . Dit formulier is bekend als Boole-logica als '[disjunctive normaal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)' (DNF). Bijvoorbeeld:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regels voor `all` :
  - Eenvoudige gelijkheids expressies kunnen niet nuttig worden gecombineerd met andere expressies. Deze expressie is bijvoorbeeld toegestaan:
    - `ratings/all(r: r eq 5)`

    maar deze expressie is niet:
    - `ratings/all(r: r eq 5 or r le 2)`

    en hoewel deze expressie is toegestaan, is het niet nuttig omdat de voor waarden elkaar overlappen:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Eenvoudige vergelijkings expressies waarbij,,, `ne` `lt` `le` `gt` of `ge` kunnen worden gecombineerd met `and` / `or` . Bijvoorbeeld:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Vergelijkings expressies in combi natie met `or` (ontkoppelingen) kunnen verder worden gecombineerd met `and` . Dit formulier is bekend als Boole-logica als '[Conjunctive normaal Form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)' (CNF). Bijvoorbeeld:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regels voor het filteren van complexe verzamelingen

Lambda-expressies over complexe verzamelingen ondersteunen een veel meer flexibele syntaxis dan lambda-expressies voor verzamelingen van primitieve typen. U kunt elke filter constructie in een dergelijke lambda-expressie gebruiken die u buiten een kunt gebruiken, met slechts twee uitzonde ringen.

Ten eerste, de functies `search.ismatch` en `search.ismatchscoring` worden niet ondersteund binnen lambda-expressies. Zie meer informatie [over OData-verzamelings filters in Azure Cognitive Search](search-query-understand-collection-filters.md).

Ten tweede is het niet toegestaan om te verwijzen naar velden die niet zijn *gebonden* aan de variabele Range (dus *gratis variabelen*). Bekijk bijvoorbeeld de volgende twee equivalente OData-filter expressies:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

De eerste expressie is toegestaan, terwijl het tweede formulier wordt afgewezen omdat het `details/margin` niet is gekoppeld aan de bereik variabele `s` .

Deze regel wordt ook uitgebreid naar expressies met variabelen die zijn gebonden in een buitenste bereik. Dergelijke variabelen zijn gratis ten opzichte van het bereik waarin ze worden weer gegeven. De eerste expressie is bijvoorbeeld toegestaan, terwijl de tweede equivalente expressie niet is toegestaan omdat deze vrij is ten opzichte `s/name` van het bereik van de variabele bereik `a` :

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Deze beperking is niet een probleem in de praktijk omdat het altijd mogelijk is om filters te maken, zodat lambda-expressies alleen gebonden variabelen bevatten.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Cheat-blad voor regels voor verzamelings filter

De volgende tabel bevat een overzicht van de regels voor het samen stellen van geldige filters voor elk verzamelings gegevens type.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Zie [geldige verzamelings filters schrijven](#bkmk_examples)voor voor beelden van het maken van geldige filters voor elke case.

Als u regel matig filters schrijft en u meer informatie wilt over de regels uit de eerste principes, kunt u meer weten [over de filters van OData-verzamelingen in Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Volgende stappen  

- [Meer informatie over OData-verzamelings filters in azure Cognitive Search](search-query-understand-collection-filters.md)
- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)