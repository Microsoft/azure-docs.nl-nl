---
title: Eenvoudige lucene-query syntaxis gebruiken
titleSuffix: Azure Cognitive Search
description: Query voorbeelden met de eenvoudige syntaxis voor zoeken in volledige tekst, filteren op filters en geo-Zoek opdrachten voor een Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694033"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Gebruik de eenvoudige Zoek syntaxis in azure Cognitive Search

In azure Cognitive Search roept de [syntaxis van de eenvoudige query](query-simple-syntax.md) de standaard query-parser aan voor zoeken in volledige tekst. De parser is snel en behandelt veelvoorkomende scenario's, zoals zoeken in volledige tekst, gefilterde en facetten zoeken en voor voegsel zoeken. In dit artikel worden voor beelden gebruikt voor het illustreren van het gebruik van eenvoudige syntaxis in een aanvraag voor [Zoek documenten (rest API)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Een alternatieve query syntaxis is [volledige lucene](query-lucene-syntax.md), waarmee complexere query structuren worden ondersteund, zoals fuzzy en zoek opdrachten met Joker tekens. Zie [de syntaxis Full lucene gebruiken](search-query-lucene-examples.md)voor meer informatie en voor beelden.

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
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ ' Search ' is ingesteld op `*` is een niet-opgegeven query die gelijk is aan Null of een lege zoek opdracht. Het is niet erg nuttig, maar het is de eenvoudigste zoek opdracht die u kunt uitvoeren. alle velden die kunnen worden opgehaald, worden weer gegeven in de index, met alle waarden.

+ ' query type ' is ingesteld op ' Simple ' is de standaard waarde en kan worden wegge laten, maar is wel opgenomen om verder te versterken dat de query voorbeelden in dit artikel in de eenvoudige syntaxis worden weer gegeven.

+ Stel set in op een door komma's gescheiden lijst met velden wordt gebruikt voor samen stelling van zoek resultaten, met inbegrip van alleen die velden die nuttig zijn in de context van de zoek resultaten.

+ ' count ' retourneert het aantal documenten dat overeenkomt met de zoek criteria. In een lege Zoek reeks zijn de aantallen alle documenten in de index (50 in het geval van hotels-voor beeld-index).

## <a name="example-1-full-text-search"></a>Voor beeld 1: zoeken in volledige tekst

Zoek opdrachten in volledige tekst kunnen elk wille keurig aantal zelfstandige voor waarden of citaten zijn, met of zonder Booleaanse Opera tors. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

Een zoek opdracht met tref woorden die bestaat uit belang rijke termen of zinsdelen is het meest geschikt voor u. Teken reeks velden worden tekst analyse tijdens het indexeren en uitvoeren van query's, het weghalen van niet-essentiële woorden zoals ' de ', ' en ', '. Als u wilt zien hoe een query reeks in de index wordt getokend, geeft u de teken reeks door in een [analyse van tekst analyseren](/rest/api/searchservice/test-analyzer) naar de index.

De para meter ' Search mode ' bepaalt de precisie en het terughalen. Als u meer intrekken wilt, gebruikt u de standaard waarde ' any ', die een resultaat retourneert als een deel van de query reeks overeenkomt. Als u de precisie wilt aanpassen, waarbij alle delen van de teken reeks moeten overeenkomen, wijzigt u Search mode in ' alle '. Voer de bovenstaande query uit om te zien hoe Search mode het resultaat wijzigt.

De reactie voor de query ' groep beveiligd-wachtwoord verificatie en lucht haven ' moet er ongeveer uitzien als in het volgende voor beeld, afgekapt voor de boog.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Let op de zoek Score in het antwoord. Dit is de relevantie Score van de overeenkomst. Standaard retourneert een zoek service de beste 50 overeenkomsten op basis van deze score.

Uniforme scores van ' 1,0 ' treden op wanneer er geen positie is, omdat de zoek opdracht niet in volledige tekst is gezocht, of omdat er geen criteria zijn ingesteld. Bijvoorbeeld, in een lege zoek opdracht (Search = `*` ), worden rijen weer gegeven in een wille keurige volg orde. Wanneer u werkelijke criteria opneemt, ziet u dat zoek scores worden weer geven in betekenis volle waarden.

## <a name="example-2-look-up-by-id"></a>Voor beeld 2: opzoeken op basis van ID

Wanneer u zoek resultaten in een query retourneert, is een logische volgende stap het opgeven van een detail pagina die meer velden uit het document bevat. Dit voor beeld laat zien hoe u één document kunt retour neren met behulp van [opzoek document](/rest/api/searchservice/lookup-document) door de document-id door te geven.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Alle documenten hebben een unieke id. Als u de portal gebruikt, selecteert u het tabblad index uit **indexen** en bekijkt u de veld definities om te bepalen welk veld de sleutel is. Als u REST gebruikt, retourneert de aanroep [index ophalen](/rest/api/searchservice/get-index) de index definitie in de hoofd tekst van het antwoord.

Het antwoord voor de bovenstaande query bestaat uit het document waarvan de sleutel 41 is. Elk veld dat is gemarkeerd als ' ophalen ' in de index definitie kan worden geretourneerd in Zoek resultaten en worden weer gegeven in uw app.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Voor beeld 3: filteren op tekst

De [filter syntaxis](search-query-odata-filter.md) is een OData-expressie die u zelf of met ' zoeken ' kunt gebruiken. Wordt samen gebruikt, wordt ' filter ' toegepast op de volledige index. vervolgens wordt de zoek opdracht uitgevoerd op de resultaten van het filter. Filters zijn dus nuttig om de resultaten van de zoekopdracht te verbeteren, doordat het aantal documenten dat moet worden doorzocht, wordt verminderd.

Filters kunnen worden gedefinieerd in elk veld dat is gemarkeerd als ' filterbaar ' in de index definitie. Voor hotels-voor beeld-index kunt u filter bare velden bevatten categorie, tags, ParkingIncluded, classificatie en de meeste adres velden.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

De reactie voor de bovenstaande query is beperkt tot alleen de hotels die zijn gecategoriseerd als ' rapport en beveiligd-wachtwoord verificatie ', waaronder de termen ' Art ' of ' rond leidingen '. In dit geval is er slechts één overeenkomst.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Voor beeld 4: filter functies

Filter expressies kunnen [' Search. ismatch ' en ' Search. ismatchscoring '](search-query-odata-full-text-search-functions.md)bevatten, zodat u een zoek query kunt bouwen in het filter. Deze filter expressie maakt *gebruik van een* Joker teken om voorzieningen te selecteren, inclusief gratis WiFi, gratis parkeren, enzovoort.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

Antwoord voor de bovenstaande query komt overeen met 19 hotels die gratis voorzieningen aanbieden. U ziet dat de zoek Score een uniform ' 1,0 ' in de resultaten heeft. Dit komt doordat de zoek expressie Null of leeg is, wat resulteert in Verbatim-filter overeenkomsten, maar geen zoek opdracht in volledige tekst. Relevantie scores worden alleen geretourneerd voor Zoek opdrachten in volledige tekst. Als u filters gebruikt zonder ' zoeken ', moet u ervoor zorgen dat u voldoende Sorteer bare velden hebt zodat u de zoek positie kunt beheren.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Voor beeld 5: bereik filters

Bereik filtering wordt ondersteund via filter expressies voor elk gegevens type. De volgende voor beelden illustreren numerieke en teken reeks bereik. Gegevens typen zijn belang rijk in bereik filters en werken het beste als numerieke gegevens zich in numerieke velden bevinden en teken reeks gegevens in teken reeks velden. Numerieke gegevens in teken reeks velden zijn niet geschikt voor bereiken, omdat numerieke teken reeksen niet vergelijkbaar zijn.

De volgende query is een numeriek bereik. In hotels-voor beeld-index is het enige filterbaar numeriek veld een classificatie.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

Antwoord voor deze query moet er ongeveer uitzien als in het volgende voor beeld, afgekapt voor de boog.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

De volgende query is een bereik filter voor een teken reeks veld (adres/StateProvince):

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

Antwoord voor deze query moet er ongeveer zo uitzien als in het onderstaande voor beeld, afgekapt voor de boog. In dit voor beeld is het niet mogelijk om te sorteren op StateProvince omdat het veld niet als ' sorteerbaar ' wordt weer in de index definitie.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Voor beeld 6: geografisch zoeken

De voor beeld-index van de hotels bevat een geo_location veld met breedte-en breedte coördinaten. In dit voor beeld wordt de [functie geo. Distance](search-query-odata-geo-spatial-functions.md#examples) gebruikt waarmee wordt gefilterd op documenten in de omtrek van een begin punt, tot een wille keurige afstand (in kilo meters) die u opgeeft. U kunt de laatste waarde in de query (10) aanpassen om de surface area van de query te verminderen of te verg Roten.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

Antwoord voor deze query retourneert alle hotels binnen een afstand van 10 kilo meter van de beschik bare coördinaten:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Voor beeld 7: booleans met Search mode

Eenvoudige syntaxis biedt ondersteuning voor Booleaanse Opera tors in de vorm van tekens ( `+, -, |` ) om de logica te ondersteunen en, of en niet op te vragen. Booleaanse zoek acties gedraagt zich zoals u kunt verwachten, met een paar uitzonde ringen. 

In de vorige voor beelden werd de para meter ' Search mode ' geïntroduceerd als een mechanisme voor het bepalen van de precisie en het intrekken van ' Search mode = any ' (een document dat aan een van de criteria voldoet, wordt beschouwd als een overeenkomst) en ' Search mode = all ' om de nauw keurigheid te bepalen (alle criteria moeten overeenkomen in een document). 

In de context van een Boole-zoek opdracht kan het standaard ' Search mode = any ' verwarrend zijn als u een query met meerdere opera tors stapelt en breder wordt in plaats van smallere resultaten. Dit is met name het geval wanneer de resultaten alle documenten bevatten die geen specifieke term of woord groep zijn.

In het volgende voor beeld ziet u een afbeelding. De volgende query uitvoeren met Search mode (any), 42-documenten worden geretourneerd: de woorden die de term ' restaurant ' bevatten, plus alle documenten die niet de zin ' Airconditioning ' hebben. 

U ziet dat er geen spatie tussen de Booleaanse operator ( `-` ) en de zin ' Airconditioning ' is.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

Als u ' Search mode = all ' wijzigt, wordt een cumulatief effect op criteria afgedwongen en wordt een kleinere resultatenset geretourneerd (7 overeenkomsten) die bestaat uit documenten met de term ' restaurant ', met inbegrip van de woorden met de zin ' Airconditioning '.

Antwoord voor deze query ziet er nu uit als in het volgende voor beeld, afgekapt voor de boog.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Voor beeld 8: paginerings resultaten

In de vorige voor beelden hebt u geleerd over para meters die van invloed zijn op de samen stelling van de zoek resultaten, waaronder "selecteren", die bepaalt welke velden een resultaat oplevert, de sorteer volgorde en hoe u een telling van alle overeenkomsten opneemt. Dit voor beeld is een voortzetting van de samen stelling van zoek resultaten in de vorm van paginerings parameters waarmee u het aantal resultaten kunt Batchen dat op een wille keurige pagina wordt weer gegeven. 

Standaard retourneert een zoek service de meest overeenkomende 50-overeenkomsten. Als u het aantal overeenkomsten op elke pagina wilt beheren, gebruikt u ' top ' om de grootte van de batch te definiëren en gebruikt u vervolgens ' overs Laan ' om volgende batches op te halen.

In het volgende voor beeld wordt een filter en sorteer volgorde gebruikt voor het veld classificatie (classificatie is zowel filterbaar als sorteerbaar), omdat het eenvoudiger is om de effecten van paginering op gesorteerde resultaten te zien. In een normale volledige Zoek query worden de beste overeenkomsten gerangschikt en gewisseld door @search.score .

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

Met de query worden 21 overeenkomende documenten gevonden, maar omdat u ' top ' hebt opgegeven, retourneert de reactie alleen de vijf meest overeenkomende treffers, met de classificaties vanaf 4,9, en eindigt om 4,7 met ' Cora van de Lake B & B '. 

Als u de volgende 5 wilt ophalen, slaat u de eerste batch over:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

Het antwoord voor de tweede batch slaat de eerste vijf overeenkomsten over, waarbij de volgende vijf worden geretourneerd, te beginnen met "Pull'r Inn Motel". Als u wilt door gaan met aanvullende batches, kunt u ' top ' op 5 houden en vervolgens ' overs Laan ' door 5 op elke nieuwe aanvraag verhogen (overs Laan = 5, overs Laan = 10, overs laan = 15, enzovoort).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Volgende stappen

Nu u een van de procedures met de basis query syntaxis hebt uitgevoerd, kunt u query's in code opgeven. In de volgende koppelingen wordt uitgelegd hoe u zoek query's kunt instellen met behulp van de Azure Sdk's.

+ [Query's uitvoeren op uw index met behulp van de .NET SDK](search-get-started-dotnet.md)
+ [Query's uitvoeren op uw index met behulp van de python-SDK](search-get-started-python.md)
+ [Query's uitvoeren op uw index met behulp van de Java script SDK](search-get-started-javascript.md)

Aanvullende Naslag informatie over syntaxis, query architectuur en voor beelden vindt u in de volgende koppelingen:

+ [Voor beelden van Lucene-syntaxis query's voor het maken van geavanceerde query's](search-query-lucene-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Vereenvoudigde querysyntaxis](query-simple-syntax.md)
+ [Volledige Lucene-querysyntaxis](query-lucene-syntax.md)
+ [Filter syntaxis](search-query-odata-filter.md)