---
title: Volledige lucene-query syntaxis gebruiken
titleSuffix: Azure Cognitive Search
description: Query voorbeelden waarmee de Lucene-query syntaxis wordt gedemonstreerd voor fuzzy zoeken, proximity Search, term boosting, reguliere expressies zoeken en zoek opdrachten met Joker tekens in een Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693557"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Gebruik de ' volledige ' lucene-Zoek syntaxis (geavanceerde query's in azure Cognitive Search)

Wanneer u query's voor Azure Cognitive Search bouwt, kunt u de standaard [eenvoudige query-parser](query-simple-syntax.md) vervangen door de krachtige [lucene query-parser](query-lucene-syntax.md) om gespecialiseerde en geavanceerde query-expressies te formuleren.

De Lucene-parser ondersteunt complexe query-indelingen, zoals query's met een veld bereik, zoek acties op zoek opdrachten, infix-en achtervoegsel joker tekens zoeken, proximity Search, term Boosting en reguliere expressie zoeken. De extra stroom wordt geleverd met extra verwerkings vereisten, zodat u een iets langere uitvoerings tijd verwacht. In dit artikel kunt u een voor beeld bekijken van het demonstreren van query bewerkingen op basis van de volledige syntaxis.

> [!Note]
> Veel van de gespecialiseerde query constructies die zijn ingeschakeld via de volledige lucene-query syntaxis, worden niet in [tekst geanalyseerd](search-lucene-query-architecture.md#stage-2-lexical-analysis), wat kan worden verrassende als u op basis van stam of lemmatisering verwacht. Lexicale analyse wordt alleen uitgevoerd op de volledige voor waarden (een term query of een woordgroepen query). Query typen met onvolledige voor waarden (voor voegsel query, Joker teken query, regex-query, fuzzy query) worden direct toegevoegd aan de query structuur, waarbij de analyse fase wordt omzeild. De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen is lowercasing. 
>

## <a name="hotels-sample-index"></a>Voor beeld-index van hotels

De volgende query's zijn gebaseerd op de voor beeld-index van hotels, die u kunt maken door de instructies in deze [Snelstartgids](search-get-started-portal.md)te volgen.

Voorbeeld query's worden gegeleeerd met behulp van de REST API en POST-aanvragen. U kunt deze plakken en uitvoeren in [postman](search-get-started-rest.md) of [Visual Studio code met de extensie Cognitive Search](search-get-started-vs-code.md).

Aanvraag headers moeten de volgende waarden hebben:

| Sleutel | Waarde |
|-----|-------|
| Content-Type | application/json|
| API-sleutel  | `<your-search-service-api-key>`, een query of beheerder sleutel |

URI-para meters moeten uw zoek service-eind punt bevatten met de index naam, documenten verzamelingen, zoek opdracht en API-versie, vergelijkbaar met het volgende voor beeld:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

De aanvraag tekst moet worden gevormd als een geldige JSON:

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ ' Search ' is ingesteld op `*` is een niet-opgegeven query die gelijk is aan Null of een lege zoek opdracht. Het is niet erg nuttig, maar het is de eenvoudigste zoek opdracht die u kunt uitvoeren. alle velden die kunnen worden opgehaald, worden weer gegeven in de index, met alle waarden.

+ ' query type ' ingesteld op ' Full ' roept de volledige lucene-query-parser aan en is vereist voor deze syntaxis.

+ Stel set in op een door komma's gescheiden lijst met velden wordt gebruikt voor samen stelling van zoek resultaten, met inbegrip van alleen die velden die nuttig zijn in de context van de zoek resultaten.

+ ' count ' retourneert het aantal documenten dat overeenkomt met de zoek criteria. In een lege Zoek reeks zijn de aantallen alle documenten in de index (50 in het geval van hotels-voor beeld-index).

## <a name="example-1-fielded-search"></a>Voor beeld 1: zoeken in een veld

Het zoek bereik dat is opgenomen in de velden persoonlijk en Inge sloten Zoek expressies naar een specifiek veld. In dit voor beeld wordt gezocht naar namen van hotels met de term ' Hotel ', maar niet ' Motel '. U kunt meerdere velden opgeven met en. 

Wanneer u deze query syntaxis gebruikt, kunt u de para meter ' searchFields ' weglaten wanneer de velden die u wilt doorzoeken, in de zoek expressie zelf staan. Als u "searchFields" opneemt met een zoek opdracht in een veld, `fieldName:searchExpression` heeft het altijd voor rang op ' searchFields '.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

Antwoord voor deze query moet er ongeveer uitzien als in het volgende voor beeld, gefilterd op ' redmiddel en beveiligd-wachtwoord verificatie ', waarbij hotels worden geretourneerd met ' Hotel ' of ' Motel ' in de naam.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

De zoek expressie kan één term of woord groep zijn, of een complexere expressie tussen haakjes, optioneel met Booleaanse Opera tors. Enkele voor beelden zijn:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Zorg ervoor dat u een woord groep tussen aanhalings tekens plaatst als u wilt dat beide teken reeksen als één entiteit worden geëvalueerd. in dit geval zoekt u naar twee verschillende locaties in het veld adres/StateProvince. Afhankelijk van de client moet u mogelijk `\` de aanhalings tekens ().

Het veld dat is opgegeven in, `fieldName:searchExpression` moet een doorzoekbaar veld zijn. Zie [Create Index (rest API)](/rest/api/searchservice/create-index) voor meer informatie over hoe veld definities worden toegeschreven.

## <a name="example-2-fuzzy-search"></a>Voor beeld 2: fuzzy Search

Fuzzy Zoek resultaten komen overeen met voor waarden die vergelijkbaar zijn, inclusief verkeerd gespelde woorden. Als u een fuzzy zoek opdracht wilt uitvoeren, voegt u het tilde- `~` symbool toe aan het einde van één woord met een optionele para meter, een waarde tussen 0 en 2, waarmee de bewerkings afstand wordt opgegeven. U kunt bijvoorbeeld `blue~` een `blue~1` blauwe, blauwe en lijm retour neren.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

Antwoord voor deze query wordt omgezet in ' Concierge ' in de overeenkomende documenten, afgekapt voor het boogere:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

Zinsdelen worden niet direct ondersteund, maar u kunt een exacte overeenkomst opgeven voor elke term van een woord groep met meerdere delen, zoals `search=Tags:landy~ AND sevic~` .  Met deze query-expressie vindt u 15 overeenkomsten op ' wasgoed-service '.

> [!Note]
> Fuzzy query's worden niet [geanalyseerd](search-lucene-query-architecture.md#stage-2-lexical-analysis). Query typen met onvolledige voor waarden (voor voegsel query, Joker teken query, regex-query, fuzzy query) worden direct toegevoegd aan de query structuur, waarbij de analyse fase wordt omzeild. De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen, is een onderste behuizing.
>

## <a name="example-3-proximity-search"></a>Voor beeld 3: proximity Search

Proximity Search zoekt naar voor waarden die zich in een document nabij elkaar bevinden. Voeg een tilde ' ~ ' aan het einde van een woord groep toe, gevolgd door het aantal woorden dat de grens van de nabijheid heeft gemaakt.

Met deze query zoekt u naar de termen ' Hotel ' en ' lucht haven ' binnen vijf woorden van elkaar in een document. De aanhalings tekens worden voorafgegaan ( `\"` ) om de woord groep te behouden:

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

Antwoord voor deze query moet er ongeveer uitzien als in het volgende voor beeld:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Voor beeld 4: term versterking

De term Boosting verwijst naar een hoger niveau voor een document als dit de gestimuleerde term bevat, ten opzichte van documenten die de term niet bevatten. Als u een periode wilt verhogen, gebruikt u het caret- `^` teken () met een boost factor (een getal) aan het einde van de term die u zoekt. De standaard waarde voor de boosts factor is 1 en hoewel deze positief moet zijn, mag deze kleiner zijn dan 1 (bijvoorbeeld 0,2). De term Boosting verschilt van Score profielen in die score profielen om bepaalde velden te stimuleren, in plaats van specifieke voor waarden.

In deze ' before '-query zoekt u naar ' strand toegang ' en ziet u dat er zeven documenten zijn die overeenkomen met een of beide voor waarden.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

Er is slechts één document dat overeenkomt met ' toegang ', en omdat dit het enige resultaat is, is de plaatsing hoog (tweede positie), zelfs als in het document de term ' strand ' ontbreekt.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

In de query ' na ' herhaalt u de zoek opdracht. deze tijd verhoogt de resultaten met de term ' strand ' over de term ' toegang '. Een door menselijke Lees bare versie van de query is `search=Description:beach^2 access` . Afhankelijk van uw client, moet u mogelijk de uitdrukken `^2` `%5E2` .

Na versterking van de term ' strand ', wordt de overeenkomst op het oude Carrabelle Hotel omlaag verplaatst naar de zesde plaats.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Voor beeld 5: regex

Een reguliere expressie zoekt zoekt naar een overeenkomst op basis van de inhoud tussen slashes '/', zoals beschreven in de [klasse RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Antwoord voor deze query moet er ongeveer uitzien als in het volgende voor beeld:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> Regex-query's worden niet [geanalyseerd](./search-lucene-query-architecture.md#stage-2-lexical-analysis). De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen, is een onderste behuizing.
>

## <a name="example-6-wildcard-search"></a>Voor beeld 6: zoeken met Joker tekens

U kunt de algemeen erkende syntaxis voor Zoek opdrachten met Joker tekens voor meerdere ( `*` ) of één ( `?` ) gebruiken. Opmerking de Lucene-query-parser ondersteunt het gebruik van deze symbolen met één term en geen woord groep.

In deze query zoekt u naar Hotel namen die het voor voegsel ' SC ' bevatten. U kunt een `*` symbool of niet gebruiken `?` als het eerste teken van een zoek opdracht.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Antwoord voor deze query moet er ongeveer uitzien als in het volgende voor beeld:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> Query's met Joker tekens worden niet [geanalyseerd](./search-lucene-query-architecture.md#stage-2-lexical-analysis). De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen, is een onderste behuizing.
>

## <a name="next-steps"></a>Volgende stappen

Probeer query's in code op te geven. In de volgende koppelingen wordt uitgelegd hoe u zoek query's kunt instellen met behulp van de Azure Sdk's.

+ [Query's uitvoeren op uw index met behulp van de .NET SDK](search-get-started-dotnet.md)
+ [Query's uitvoeren op uw index met behulp van de python-SDK](search-get-started-python.md)
+ [Query's uitvoeren op uw index met behulp van de Java script SDK](search-get-started-javascript.md)

Aanvullende Naslag informatie over syntaxis, query architectuur en voor beelden vindt u in de volgende koppelingen:

+ [Voor beelden van Lucene-syntaxis query's voor het maken van geavanceerde query's](search-query-lucene-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Vereenvoudigde querysyntaxis](query-simple-syntax.md)
+ [Volledige Lucene-querysyntaxis](query-lucene-syntax.md)
+ [Filter syntaxis](search-query-odata-filter.md)