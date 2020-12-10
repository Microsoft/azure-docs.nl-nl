---
title: Typen query's
titleSuffix: Azure Cognitive Search
description: Meer informatie over de typen query's die worden ondersteund in Cognitive Search, zoals vrije tekst, filter, automatisch aanvullen en suggesties, geo-zoeken, systeem query's en zoeken naar documenten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007854"
---
# <a name="query-types-in-azure-cognitive-search"></a>Query typen in azure Cognitive Search

In azure Cognitive Search is een query een volledige specificatie van een round-trip-bewerking, met para meters die de uitvoering van query's bepalen en para meters die het antwoord vormen.

## <a name="elements-of-a-request"></a>Elementen van een aanvraag

Het volgende voor beeld is een representatieve query die is gemaakt met behulp van [Zoek documenten rest API](/rest/api/searchservice/search-documents). In dit voor beeld wordt de index voor de [Hotels-demo](search-get-started-portal.md) en de algemene para meters opgenomen, zodat u kunt zien hoe een query eruit ziet.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Query's worden altijd door gestuurd bij de verzameling documenten van één index. U kunt geen indexen samen voegen of aangepaste of tijdelijke gegevens structuren maken als een query doel.

+ **`queryType`** Hiermee stelt u de parser in, ofwel de [standaard-query-parser](search-query-simple-examples.md) (optimaal voor zoeken in volledige tekst), of de [volledige lucene-query-parser](search-query-lucene-examples.md) die wordt gebruikt voor geavanceerde query constructies, zoals reguliere expressies, proximity Search, fuzzy en Joker tekens zoeken.

+ **`search`** biedt de match criteria, meestal hele termen of zinsdelen, maar vaak vergezeld van Booleaanse Opera tors. Enkele zelfstandige termen zijn *term* query's. Query's met meerdere delen van citaten zijn *woordgroepen* query's. De zoek opdracht kan niet worden gedefinieerd, zoals in **`search=*`** , maar er is geen criteria om op te zoeken. de resultatenset bestaat uit wille keurig geselecteerde documenten.

+ **`searchFields`** beperkt de uitvoering van query's naar specifieke velden. Elk veld waarvoor een *zoekbaar* kenmerk is in het index schema, is een kandidaat voor deze para meter.

Antwoorden worden ook gevormd door de para meters die u opneemt in de query:

+ **`select`** geeft aan welke velden moeten worden geretourneerd in het antwoord. Alleen velden die zijn gemarkeerd als *ophalen* in de index kunnen worden gebruikt in een SELECT-instructie.

+ **`top`** retourneert het opgegeven aantal best overeenkomende documenten. In dit voor beeld worden slechts tien hits geretourneerd. Als u de resultaten wilt weer geven, kunt u met de bovenkant en de pagina overs Laan.

+ **`count`** geeft aan hoeveel documenten in de volledige index in zijn geheel overeenkomen, wat meer kan zijn dan wat er wordt geretourneerd. 

+ **`orderby`** wordt gebruikt als u resultaten wilt sorteren op een waarde, zoals een classificatie of locatie. Anders is de standaard waarde het gebruik van de relevantie score om de resultaten te classificeren.

> [!Tip]
> Voordat u code schrijft, kunt u query hulpprogramma's gebruiken om te zien wat de syntaxis is en experimenteren met verschillende para meters. De snelste benadering is het ingebouwde Portal-hulp programma, [Search Explorer](search-explorer.md).
>
> Als u deze Quick Start hebt uitgevoerd [om de hotels-demo-index te maken](search-get-started-portal.md), kunt u deze query reeks plakken in de zoek balk van de Explorer om uw eerste query uit te voeren: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>Hoe veld kenmerken in een index query gedrag bepalen

Het ontwerp van de index en het query ontwerp zijn nauw verbonden met Azure Cognitive Search. Een essentieel feit dat u vooraf moet weten, is dat het *index schema*, met kenmerken voor elk veld, bepaalt welk soort query u kunt maken. 

Index kenmerken voor een veld stel de toegestane bewerkingen in: Hiermee stelt u in dat een veld in de index kan worden *doorzocht* en kan worden *opgehaald* , *gesorteerd*, *gefilterd*, enzovoort. In de voorbeeld query teken reeks `"$orderby": "Rating"` werkt alleen omdat het classificatie veld is gemarkeerd als *sorteerbaar* in het index schema. 

![Index definitie voor het voor beeld van een hotel](./media/search-query-overview/hotel-sample-index-definition.png "Index definitie voor het voor beeld van een hotel")

De bovenstaande scherm afbeelding is een gedeeltelijke lijst met index kenmerken voor het voor beeld van hotels. U kunt het volledige index schema weer geven in de portal. Zie [Create index rest API](/rest/api/searchservice/create-index)voor meer informatie over index kenmerken.

> [!Note]
> Sommige query functies zijn in index-breed en niet per veld. Deze mogelijkheden omvatten: [synoniemen](search-synonyms.md), [aangepaste analyse](index-add-custom-analyzers.md)functies, [suggestie constructies (voor automatisch aanvullen en voorgestelde query's)](index-add-suggesters.md), [Score logica voor classificatie resultaten](index-add-scoring-profiles.md).

## <a name="choose-a-parser-simple--full"></a>Kies een parser: eenvoudig | waard

Azure Cognitive Search biedt u een keuze tussen twee query-parsers voor het verwerken van normale en gespecialiseerde query's. Aanvragen die gebruikmaken van de eenvoudige parser worden geformuleerd met behulp van de [eenvoudige query syntaxis](query-simple-syntax.md), geselecteerd als de standaard waarde voor de snelheid en effectiviteit in vrije-tekst query's. Deze syntaxis ondersteunt een aantal veelgebruikte Zoek operatoren, waaronder de Opera tors en, of, niet, frase, achtervoegsel en voor rang.

De [volledige lucene-query syntaxis](query-Lucene-syntax.md#bkmk_syntax), ingeschakeld wanneer u `queryType=full` aan de aanvraag toevoegt, geeft de uitgebreide en duidelijke query taal weer die is ontwikkeld als onderdeel van [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). De volledige syntaxis breidt de eenvoudige syntaxis uit. Alle query's die u voor de eenvoudige syntaxis schrijft, worden uitgevoerd onder de volledige lucene-parser. 

In de volgende voor beelden ziet u het punt: dezelfde query, maar met verschillende query type-instellingen kunt u verschillende resultaten opleveren. In de eerste query wordt de `^3` After `historic` beschouwd als onderdeel van de zoek term. Het hoogste geclassificeerde resultaat voor deze query is "Marquis Plaza & Suites", die *Oceaan* in de beschrijving heeft.

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Dezelfde query die gebruikmaakt van de volledige lucene-parser, interpreteert `^3` als een in-Field Booster. Switch parsers wijzigen de positie, met de resultaten met de term *historisch* naar boven.

```http
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typen query's

Azure Cognitive Search ondersteunt een breed scala aan query typen. 

| Query type | Gebruik | Voor beelden en meer informatie |
|------------|--------|-------------------------------|
| Zoek opdracht in vrije tekst | Zoek parameter en beide parsers| Zoek opdrachten in volledige tekst scans voor een of meer voor waarden in alle *Doorzoek* bare velden in uw index en werkt op de manier waarop u een zoek machine zoals Google of Bing verwacht. Het voor beeld in de inleiding is zoeken in volledige tekst.<br/><br/>Zoek opdracht in volledige tekst ondergaate lexicale analyse met behulp van de standaard-lucene Analyzer (standaard) om alle voor waarden kleine letters te verlagen, verwijder stop woorden zoals ' de '. U kunt de standaard instelling overschrijven met [niet-Engelse analyse](index-add-language-analyzers.md#language-analyzer-list) functies of [gespecialiseerde neutraal-](index-add-custom-analyzers.md#AnalyzerTable) analyse functies waarmee lexicale analyses worden gewijzigd. Een voor beeld is een [tref woord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) dat de volledige inhoud van een veld als één token behandelt. Dit is handig voor gegevens zoals post codes, Id's en sommige product namen. | 
| Gefilterde zoek opdracht | [OData-filter expressie](query-odata-filter-orderby-syntax.md) en een van beide parsers | Met filter query's wordt een booleaanse expressie voor alle *filter* bare velden in een index geëvalueerd. In tegens telling tot zoeken komt een filter query overeen met de exacte inhoud van een veld, inclusief hoofdletter gevoeligheid voor teken reeks velden. Een ander verschil is dat filter query's worden weer gegeven in de OData-syntaxis. <br/>[Voor beeld van filter expressie](search-query-simple-examples.md#example-3-filter-queries) |
| Op geografische locaties zoeken | Het [type EDM. GeographyPoint](/rest/api/searchservice/supported-data-types) in het veld, filter expressie en beide parsers | Coördinaten die zijn opgeslagen in een veld met een EDM. GeographyPoint worden gebruikt voor de besturings elementen "dichtbij zoeken" of op basis van een kaart. <br/>[Voor beeld van geo-zoeken](search-query-simple-examples.md#example-5-geo-search)|
| Bereik zoeken | filter expressie en eenvoudige parser | In azure Cognitive Search worden bereik query's gemaakt met behulp van de filter parameter. <br/>[Voor beeld van Range-filter](search-query-simple-examples.md#example-4-range-filters) | 
| [Zoek opdracht in veld](query-lucene-syntax.md#bkmk_fields) | Zoek parameter en volledige parser | Bouw een samengestelde query-expressie die is gericht op één veld. <br/>[Voor beeld van een zoek opdracht naar een veld](search-query-lucene-examples.md#example-2-fielded-search) |
| [Automatisch aanvullen of voorgestelde resultaten](search-autocomplete-tutorial.md) | De para meter voor automatisch aanvullen of suggestie | Een alternatief query formulier dat wordt uitgevoerd op basis van gedeeltelijke teken reeksen in een zoek-naar-u-type-ervaring. U kunt automatisch aanvullen en suggesties samen of afzonderlijk gebruiken. |
| [Zoek actie op fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Zoek parameter en volledige parser | Komt overeen met de termen met een vergelijk bare constructie of spelling. <br/>[Voor beeld van fuzzy zoeken](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [Proximity Search](query-lucene-syntax.md#bkmk_proximity) | Zoek parameter en volledige parser | Hiermee worden zoek termen in een document in de buurt. <br/>[Voor beeld van proximity Search](search-query-lucene-examples.md#example-4-proximity-search) |
| [term versterking](query-lucene-syntax.md#bkmk_termboost) | Zoek parameter en volledige parser | Hiermee wordt een document hoger gerangschikt als het de gestimuleerde term bevat, ten opzichte van andere. <br/>[Voor beeld van een betere term](search-query-lucene-examples.md#example-5-term-boosting) |
| [reguliere expressie zoeken](query-lucene-syntax.md#bkmk_regex) | Zoek parameter en volledige parser | Komt overeen met de inhoud van een reguliere expressie. <br/>[Voor beeld van een reguliere expressie](search-query-lucene-examples.md#example-6-regex) |
|  [Joker teken of voor voegsel zoeken](query-lucene-syntax.md#bkmk_wildcard) | Zoek parameter en volledige parser | Komt overeen met een voor voegsel en tilde () `~` of één teken ( `?` ). <br/>[Zoek voorbeeld voor joker tekens](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Volgende stappen

Gebruik de portal of een ander hulp programma zoals postman of Visual Studio code of een van de Sdk's om query's gedetailleerder te verkennen. Met de volgende koppelingen kunt u aan de slag.

+ [Zoek Verkenner](search-explorer.md)
+ [Query's uitvoeren in .NET](./search-get-started-dotnet.md)
+ [Query's uitvoeren in REST](./search-get-started-powershell.md)