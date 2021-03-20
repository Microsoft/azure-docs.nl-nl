---
title: Filteren op zoek resultaten
titleSuffix: Azure Cognitive Search
description: Filteren op beveiligings identiteit, taal, geografische locatie of numerieke waarden van de gebruiker om Zoek resultaten te verminderen voor query's in azure Cognitive Search, een gehoste service voor zoeken in de Cloud op Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a5c8f835d44896a452a945614332dcbc25ca8bb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694424"
---
# <a name="filters-in-azure-cognitive-search"></a>Filters in azure Cognitive Search 

Een *filter* biedt criteria op basis van waarden voor het selecteren van documenten die worden gebruikt in een query. Een filter kan één waarde of een OData- [filter expressie](search-query-odata-filter.md)zijn. In tegens telling tot zoeken in volledige tekst, retourneert een filter waarde of expressie alleen een strikte overeenkomst.

Sommige zoek functies, zoals [facet navigatie](search-filters-facets.md), zijn afhankelijk van filters als onderdeel van de implementatie, maar u kunt filters op elk gewenst moment gebruiken om een query op specifieke waarden te bereiken. Als u in plaats daarvan een query op specifieke velden wilt maken, zijn er alternatieve methoden die hieronder worden beschreven.

## <a name="when-to-use-a-filter"></a>Wanneer u een filter wilt gebruiken

Filters zijn basis voor verschillende zoek functies, waaronder ' zoeken in de buurt ', facet navigatie en beveiligings filters die alleen de documenten tonen die een gebruiker mag zien. Als u een van deze functies implementeert, is een filter vereist. Het is het filter dat is gekoppeld aan de zoek query die de geolocatie coördinaten levert, de facet categorie die door de gebruiker is geselecteerd of de beveiligings-ID van de aanvrager.

Veelvoorkomende scenario's zijn onder andere:

+ Segmenteer Zoek resultaten op basis van inhoud in de index. Op basis van een schema met Hotel locatie, categorieën en voorzieningen, kunt u een filter maken om expliciet te voldoen aan criteria (in Seattle, op het water, met een weer gave). 

+ Een zoek ervaring implementeren met een filter vereiste:

  + [Facet navigatie](search-faceted-navigation.md) maakt gebruik van een filter om de facet categorie die door de gebruiker is geselecteerd, terug te geven.
  + Geo-search maakt gebruik van een filter om de coördinaten van de huidige locatie door te geven in apps die dichtbij zoeken. 
  + [Beveiligings filters](search-security-trimming-for-azure-search.md) geven beveiligings-id's door als filter criteria, waarbij een overeenkomst in de index fungeert als een proxy voor toegangs rechten voor het document.

+ Een "cijfers zoeken". Numerieke velden kunnen worden opgehaald en worden weer gegeven in Zoek resultaten, maar ze zijn niet doorzoekbaar (onderhevig aan Zoek opdrachten in volledige tekst). Als u selectie criteria op basis van numerieke gegevens nodig hebt, gebruikt u een filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatieve methoden voor het verkleinen van het bereik

Als u een beperkend effect wilt hebben in de zoek resultaten, zijn de filters niet uw enige keuze. Deze alternatieven kunnen beter passen, afhankelijk van uw doel stelling:

+ `searchFields` query parameter beperkt zoeken naar specifieke velden. Als uw index bijvoorbeeld afzonderlijke velden voor Engelse en Spaanse beschrijvingen bevat, kunt u searchFields gebruiken om te richten welke velden u moet gebruiken voor het zoeken in volledige tekst. 

+ `$select` para meter wordt gebruikt om op te geven welke velden in een resultatenset moeten worden meegenomen, waardoor het antwoord effectief wordt afgekapt voordat het naar de aanroepende toepassing wordt verzonden. Met deze para meter wordt de query niet verfijnd of wordt de document verzameling niet verminderd, maar als een kleiner antwoord uw doel is, is deze para meter een optie om rekening mee te houden. 

Zie [zoeken naar documenten > aanvragen > query parameters](/rest/api/searchservice/search-documents#query-parameters)voor meer informatie over para meters.

## <a name="how-filters-are-executed"></a>Hoe filters worden uitgevoerd

Bij het uitvoeren van query's accepteert een filter-parser criteria als invoer, zet de expressie om in atomische Boole-expressies die worden weer gegeven als een structuur en evalueert vervolgens de filter structuur over filter bare velden in een index.

Filteren vindt in combi natie met zoeken plaats in de documenten die in de downstream-verwerking moeten worden meegenomen voor het ophalen van documenten en relevantie punten. Bij het koppelen met een zoek reeks, vermindert het filter de ingetrokken set van de volgende zoek bewerking effectief. Wanneer u alleen gebruikt (bijvoorbeeld wanneer de query reeks leeg is `search=*` ), is de filter criteria de enige invoer. 

## <a name="defining-filters"></a>Filters definiëren

Filters zijn OData-expressies, die worden gegeleeerd in de [filter syntaxis](search-query-odata-filter.md) die wordt ondersteund door Cognitive Search.

U kunt voor elke **Zoek** bewerking één filter opgeven, maar het filter zelf kan meerdere velden bevatten, meerdere criteria en als u een functie **ismatch** gebruikt, meerdere zoek expressies in volledige tekst. In een filter expressie met meerdere delen kunt u predikaten in een wille keurige volg orde opgeven (onderhevig aan de voor waarden van de operator prioriteit). Er is geen merk bare prestatie verbetering als u predikaten probeert te rangschikken in een bepaalde volg orde.

Een van de limieten voor een filter expressie is de maximale grootte van de aanvraag. De volledige aanvraag, inclusief het filter, kan Maxi maal 16 MB aan POST of 8 KB voor GET zijn. Er is ook een limiet voor het aantal componenten in uw filter expressie. Als u honderden componenten hebt, is het een goed idee om de limiet uit te voeren. We raden u aan uw toepassing zodanig te ontwerpen dat er geen filters van een ongebonden grootte worden gegenereerd.

De volgende voor beelden vertegenwoordigen prototypen filter definities in verschillende Api's.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Gebruiks patronen filteren

De volgende voor beelden illustreren verschillende gebruiks patronen voor filter scenario's. Zie [OData-expressie syntaxis > voor beelden](./search-query-odata-filter.md#examples)voor meer ideeën.

+ Zelfstandige **$filter**, zonder query teken reeks, handig wanneer de filter expressie volledig kan kwalificeren op interessante documenten. Zonder een query reeks is er geen lexicale of linguïstische analyse, geen scoreing en geen classificatie. U ziet dat de zoek teken reeks slechts een sterretje is, wat betekent dat alle documenten overeenkomen.

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ Combi natie van query teken reeks en **$filter**, waarbij het filter de subset maakt en de query reeks voorziet in de term invoer voor zoeken in volledige tekst via de gefilterde subset. Het toevoegen van voor waarden (wandel-afstands-theaters) introduceert Zoek punten in de resultaten, waarbij documenten die het beste overeenkomen met de voor waarden, hoger worden gerangschikt. Het gebruik van een filter met een query reeks is het meest voorkomende gebruiks patroon.

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>Overeenkomst voor hostels die hoger is dan 4 of 5 sterren motels.
   $filter = search. ismatchscoring (' Hostel ') en rating ge 4 of Search. ismatchscoring (' Motel ') en rating EQ 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>Komt overeen met ' luxe ' of ' high-end ' in het veld Beschrijving of op categorie die precies gelijk is aan ' luxe '.
   $filter = search. ismatchscoring (' luxe | high-end ', ' description ') of Category EQ ' luxe ' &$count = True
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter = search. ismatchscoring (' pool ') en classificatie ge 4

  Search = pool&$filter = classificatie ge 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Language filters](search-filters-language.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Het maken van een bestaand veld filteren

U kunt bestaande velden niet wijzigen om ze te kunnen filteren. In plaats daarvan moet u een nieuw veld toevoegen of de index opnieuw samen stellen. Zie [een Azure Cognitive search-index opnieuw samen stellen](search-howto-reindex.md)voor meer informatie over het opnieuw opbouwen van een index of het opnieuw invullen van velden.

## <a name="text-filter-fundamentals"></a>Grond beginselen van tekst filters

Tekst filters komen overeen met teken reeks velden op basis van letterlijke teken reeksen die u in het filter opgeeft: `$filter=Category eq 'Resort and Spa'`

In tegens telling tot zoeken in volledige tekst is er geen lexicale analyse of woord afbreking voor tekst filters, zodat vergelijkingen alleen voor exacte overeenkomsten gelden. Stel bijvoorbeeld dat een veld *f* de waarde ' zonnige dag ' bevat, `$filter=f eq 'Sunny'` niet overeenkomt met, maar `$filter=f eq 'sunny day'` wel. 

Teken reeksen zijn hoofdletter gevoelig. Er bevindt zich geen kleine letters in hoofd letters: er wordt `$filter=f eq 'Sunny day'` geen ' zonnige dag ' gevonden.

### <a name="approaches-for-filtering-on-text"></a>Benaderingen voor het filteren op tekst

| Methode | Beschrijving | Wanneer gebruikt u dit? |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Een functie die overeenkomt met een veld in een gescheiden lijst met teken reeksen. | Aanbevolen voor [beveiligings filters](search-security-trimming-for-azure-search.md) en voor filters waarbij veel onbewerkte tekst waarden moeten overeenkomen met een teken reeks veld. De functie **Search.in** is ontworpen voor snelheid en is veel sneller dan expliciet het veld vergelijken met de teken reeks met behulp van `eq` en `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Een functie waarmee u Zoek opdrachten in volledige tekst kunt combi neren met strikt Booleaanse filter bewerkingen in dezelfde filter-expressie. | Gebruik **Search. ismatch** (of het equivalente Score-item **Search. ismatchscoring**) als u meerdere zoek filter combinaties wilt gebruiken in één aanvraag. U kunt deze ook voor een *contains* -filter gebruiken om te filteren op een gedeeltelijke teken reeks binnen een grotere teken reeks. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Een door de gebruiker gedefinieerde expressie die bestaat uit velden, Opera tors en waarden. | Gebruik deze instelling als u exacte overeenkomsten wilt zoeken tussen een teken reeks veld en een teken reeks waarde. |

## <a name="numeric-filter-fundamentals"></a>Basis beginselen van numerieke filters

Numerieke velden bevinden zich niet `searchable` in de context van zoeken in volledige tekst. Alleen teken reeksen zijn onderhevig aan Zoek opdrachten in volledige tekst. Als u bijvoorbeeld 99,99 als zoek term invoert, ontvangt u geen back-upitems van $99,99. In plaats daarvan ziet u items met het nummer 99 in de teken reeks velden van het document. Als u numerieke gegevens hebt, moet u er dus voor zorgen dat u deze gebruikt voor filters, met inbegrip van bereiken, facetten, groepen enzovoort. 

Documenten die numerieke velden (prijs, grootte, SKU, ID) bevatten, bieden deze waarden in Zoek resultaten als het veld is gemarkeerd `retrievable` . Het punt hier is dat zoeken in volledige tekst zelf niet van toepassing is op numerieke veld typen.

## <a name="next-steps"></a>Volgende stappen

Probeer eerst de **Verkenner** in de portal te doorzoeken om query's met **$filter** -para meters te verzenden. De voor [beeld-index van onroerend](search-get-started-portal.md) goed levert interessante resultaten voor de volgende gefilterde query's wanneer u ze in de zoek balk plakt:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Als u meer voor beelden wilt gebruiken, raadpleegt u de syntaxis van de [OData-filter expressie > voor beelden](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Zie ook

+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API voor documenten zoeken](/rest/api/searchservice/search-documents)
+ [Vereenvoudigde querysyntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene-query syntaxis](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Ondersteunde gegevenstypen](/rest/api/searchservice/supported-data-types)