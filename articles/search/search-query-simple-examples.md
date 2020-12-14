---
title: Eenvoudige lucene-query syntaxis gebruiken
titleSuffix: Azure Cognitive Search
description: Lees dit voor beeld door query's uit te voeren op basis van de eenvoudige syntaxis voor zoeken in volledige tekst, filteren op filters, geo-Zoek opdrachten, facet zoeken op basis van een Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401743"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Gebruik de eenvoudige Zoek syntaxis in azure Cognitive Search

In azure Cognitive Search roept de [syntaxis van de eenvoudige query](query-simple-syntax.md) de standaard query-parser aan voor zoeken in volledige tekst. De parser is snel en behandelt veelvoorkomende scenario's, zoals zoeken in volledige tekst, gefilterde en facetten zoeken en voor voegsel zoeken. In dit artikel worden voor beelden gebruikt voor het illustreren van het gebruik van eenvoudige syntaxis in een aanvraag voor [Zoek documenten (rest API)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Een alternatieve query syntaxis is [volledige lucene](query-lucene-syntax.md), waarmee complexere query structuren worden ondersteund, zoals fuzzy en zoek opdrachten met Joker tekens. Zie [de syntaxis Full lucene gebruiken](search-query-lucene-examples.md)voor meer informatie en voor beelden.

## <a name="nyc-jobs-examples"></a>Voor beelden van NYC-taken

De volgende voor beelden maken gebruik van de [zoek index](https://azjobsdemo.azurewebsites.net/) van de NYC-taken die bestaat uit taken die beschikbaar zijn op basis van een gegevensset die wordt verschaft door de [stad van New York open data Initiative](https://nycopendata.socrata.com/). Deze gegevens mogen niet als actueel of volledig worden beschouwd. De index bevindt zich op een sandbox-service van micro soft. Dit betekent dat u geen Azure-abonnement of Azure Cognitive Search nodig hebt om deze query's uit te proberen.

Wat u nodig hebt, is postman of een gelijkwaardig hulp programma voor het uitgeven van een HTTP-aanvraag op GET of POST. Als u niet bekend bent met deze hulpprogram ma's, raadpleegt u [Quick Start: Azure Cognitive Search verkennen rest API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>De aanvraag instellen

1. Aanvraag headers moeten de volgende waarden hebben:

   | Sleutel | Waarde |
   |-----|-------|
   | Content-Type | `application/json`|
   | API-sleutel  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (dit is de daad werkelijke query-API-sleutel voor de sandbox-zoek service die als host fungeert voor de index van de NYC-taken) |

1. Stel de bewerking in op **`GET`** .

1. Stel de URL in op **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + De documenten verzameling in de index bevat alle Doorzoek bare inhoud. De query-API-sleutel die in de aanvraag header is gegeven, werkt alleen voor lees bewerkingen die zijn gericht op de verzameling documenten.

   + **`$count=true`** retourneert een telling van de documenten die voldoen aan de zoek criteria. In een lege Zoek reeks zijn de aantallen alle documenten in de index (ongeveer 2558 in het geval van NYC-taken).

   + **`search=*`** is een niet-opgegeven query die gelijk is aan Null of een lege zoek opdracht. Het is niet erg nuttig, maar het is de eenvoudigste zoek opdracht die u kunt uitvoeren. alle velden die kunnen worden opgehaald, worden weer gegeven in de index, met alle waarden.

1. Plak, als een verificatie stap, de volgende aanvraag in GET en klik op **verzenden**. Resultaten worden geretourneerd als uitgebreide JSON-documenten.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Eenvoudig parseren van query's aanroepen

Voor interactieve query's hoeft u niets op te geven: eenvoudig is de standaard waarde. Als u in code eerder hebt aangeroepen **`queryType=full`** , kunt u de standaard waarde opnieuw instellen met **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>Voor beeld 1: zoeken in volledige tekst in specifieke velden

Dit eerste voor beeld is geen parser-specifiek, maar we leiden ernaar om het eerste fundamentele query concept te introduceren: containment. In dit voor beeld worden zowel de uitvoering van query's als de reactie op slechts enkele specifieke velden beperkt. Het is belang rijk dat u weet hoe u een lees bare JSON-respons structureert wanneer uw hulp programma postman of Search Explorer is. 

Deze query streeft alleen naar *business_title* in **`searchFields`** , waarbij via de **`select`** para meter hetzelfde veld in het antwoord wordt opgegeven.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Postman-voorbeeld antwoord" border="false":::

Mogelijk hebt u de zoek Score in het antwoord gezien. Een uniforme Score van **1** treedt op als er geen positie is, omdat de zoek opdracht niet in volledige tekst is gezocht, of omdat er geen criteria zijn ingesteld. Voor een lege zoek opdracht worden rijen in een wille keurige volg orde weer gegeven. Wanneer u werkelijke criteria opneemt, ziet u dat zoek scores worden weer geven in betekenis volle waarden.

## <a name="example-2-look-up-by-id"></a>Voor beeld 2: opzoeken op basis van ID

Wanneer u zoek resultaten in een query retourneert, is een logische volgende stap het opgeven van een detail pagina die meer velden uit het document bevat. Dit voor beeld laat zien hoe u één document kunt retour neren met behulp van een [opzoek bewerking](/rest/api/searchservice/lookup-document) die in de document-id wordt door gegeven.

Alle documenten hebben een unieke id. Als u de syntaxis voor een opzoek query wilt uitproberen, moet u eerst een lijst met document-Id's retour neren, zodat u er een kunt vinden om te gebruiken. Voor NYC-taken worden de id's in het veld opgeslagen `id` .

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Vervolgens haalt u een document op uit de verzameling op basis van `id` ' 9E1E3AF9-0660-4E00-AF51-9B654925A2D5 ', dat het eerst in het vorige antwoord voor komt. Met de volgende query worden alle ophaalbaar velden voor het hele document geretourneerd.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>Voor beeld 3: query's filteren

[Filter syntaxis](./search-query-odata-filter.md) is een OData-expressie die u zelf kunt gebruiken of met **`search`** . Een zelfstandig filter, zonder een zoek parameter, is handig wanneer de filter expressie volledig kan kwalificeren op interessante documenten. Zonder een query reeks is er geen lexicale of linguïstische analyse, geen Score (alle scores zijn 1) en geen classificatie. U ziet dat de zoek teken reeks leeg is.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Samen gebruikt, wordt het filter eerst op de volledige index toegepast, waarna de zoek opdracht wordt uitgevoerd op de resultaten van het filter. Filters zijn dus nuttig om de resultaten van de zoekopdracht te verbeteren, doordat het aantal documenten dat moet worden doorzocht, wordt verminderd.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Query-antwoord filteren" border="false":::

Een andere krachtige manier om filters en zoek opdrachten te combi neren, is via **`search.ismatch*()`** een filter expressie, waarin u een zoek query kunt gebruiken in het filter. Deze filter expressie maakt gebruik van een Joker teken op het *abonnement* om business_title te selecteren, inclusief de term plan, planner, planning, enzovoort.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Zie [Search. ismatch in ' filter voorbeelden '](./search-query-odata-full-text-search-functions.md#examples)voor meer informatie over de functie.

## <a name="example-4-range-filters"></a>Voor beeld 4: bereik filters

Bereik filtering wordt ondersteund via **`$filter`** expressies voor elk gegevens type. In de volgende voor beelden wordt gezocht naar numerieke en teken reeks velden. 

Gegevens typen zijn belang rijk in bereik filters en werken het beste als numerieke gegevens zich in numerieke velden bevinden en teken reeks gegevens in teken reeks velden. Numerieke gegevens in teken reeks velden zijn niet geschikt voor bereiken, omdat numerieke teken reeksen niet vergelijkbaar zijn in azure Cognitive Search.

De volgende query is een numeriek bereik:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Bereik filter voor numerieke bereiken" border="false":::

In deze query is het bereik een teken reeks veld (business_title):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Bereik filter voor tekstbereiken" border="false":::

> [!NOTE]
> Facet overschrijding van bereik waarden is een algemene vereiste voor het zoeken van toepassingen. Zie [een facet filter bouwen](search-filters-facets.md)voor meer informatie en voor beelden.

## <a name="example-5-geo-search"></a>Voor beeld 5: geografisch zoeken

De voor beeld-index bevat een geo_location veld met de breedte graad en lengte graad. In dit voor beeld wordt de [functie geo. Distance](search-query-odata-geo-spatial-functions.md#examples) gebruikt waarmee wordt gefilterd op documenten in de omtrek van een begin punt, tot een wille keurige afstand (in kilo meters) die u opgeeft. U kunt de laatste waarde in de query (4) aanpassen om de surface area van de query te verkleinen of te verg Roten.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

Voor meer Lees bare resultaten worden de zoek resultaten afgekapt met de functie en de werk locatie. De begin coördinaten zijn verkregen van een wille keurig document in de index (in dit geval voor een werk locatie op Staten-eiland).

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Kaart van Staten-eiland" border="false":::

## <a name="example-6-search-precision"></a>Voor beeld 6: Zoek precisie

Term query's zijn enkele termen, misschien veel hiervan, die onafhankelijk van elkaar worden geëvalueerd. Woordgroepen query's worden tussen aanhalings tekens geplaatst en geëvalueerd als een Verbatim teken reeks. De nauw keurigheid van de overeenkomst wordt bepaald door Opera tors en Search mode.

Voor beeld 1: `search=fire`  komt overeen met 140 resultaten, waarbij alle overeenkomsten het woord ergens in het document kunnen bevatten.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

Voor beeld 2: `search=fire department` retourneert 2002 resultaten. Er worden overeenkomsten geretourneerd voor documenten die hetzij brand of Department bevatten.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

Voor beeld 3: `search="fire department"` retourneert 77 resultaten. Als u de teken reeks tussen aanhalings tekens insluit, wordt een woordgroepen zoekopdracht gemaakt die bestaat uit beide termen en worden er overeenkomsten gevonden voor termen met een token in de index die uit de gecombineerde termen bestaan. Dit legt uit waarom een zoek actie als `search=+fire +department` niet gelijk is. Beide termen zijn vereist, maar worden onafhankelijk gescand. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Omdat een woordgroepen query is opgegeven via aanhalings tekens, wordt in dit voor beeld een escape-teken ( `\` ) toegevoegd om de syntaxis te behouden.

## <a name="example-7-booleans-with-searchmode"></a>Voor beeld 7: booleans met Search mode

Eenvoudige syntaxis ondersteunt Booleaanse Opera tors in de vorm van tekens ( `+, -, |` ). De para meter Search mode informeert de afwegingen tussen Precision en intrekken, met als voor keur **`searchMode=any`** intrekken (overeenkomt met een criterium dat in aanmerking komt voor een document voor de resultatenset), en om te voldoen aan de **`searchMode=all`** nauw keurigheid (alle criteria moeten overeenkomen). 

De standaard waarde is **`searchMode=any`** , wat verwarrend kan zijn als u een query stapelt met meerdere opera tors en brederere resultaten ophaalt. Dit is met name het geval bij niet, waarbij alle resultaten alle documenten bevatten die geen specifieke term zijn.

Met behulp van de standaard Search mode (alle) worden 2800-documenten geretourneerd: degene die de zinsnede ' Fire Department ' bevat, plus alle documenten die niet de zin ' Metrotech Center ' hebben.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="Zoek modus any" border="false":::

Als u wijzigingen aanbrengt **`searchMode=all`** , wordt een cumulatief effect op criteria afgedwongen en wordt er een kleinere set resultaten weer gegeven-21 documenten-die bestaan uit documenten met de volledige zin "brand Department", min die taken op het Metrotech Center-adres.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="Zoek modus Alles" border="false":::

## <a name="example-8-structuring-results"></a>Voor beeld 8: resultaten structureren

Verschillende para meters bepalen welke velden worden weer gegeven in de zoek resultaten, het aantal geretourneerde documenten in elke batch en de sorteer volgorde. In dit voor beeld worden enkele van de voor gaande voor beelden opnieuw weer gegeven, waarbij de resultaten worden beperkt tot specifieke velden met de **`$select`** zoek criteria voor de instructie en Verbatim, waarbij 82 overeenkomsten worden geretourneerd.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

In het vorige voor beeld kunt u sorteren op titel. Deze sortering werkt omdat civil_service_title in de index moet worden *gesorteerd* .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

Paginerings resultaten worden geïmplementeerd met behulp **`$top`** van de para meter. in dit geval worden de vijf beste documenten geretourneerd:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

Als u de volgende 5 wilt ophalen, slaat u de eerste batch over:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>Volgende stappen

Probeer query's in code op te geven. In de volgende koppelingen wordt uitgelegd hoe u zoek query's kunt instellen met behulp van de Azure Sdk's.

+ [Query's uitvoeren op uw index met behulp van de .NET SDK](search-get-started-dotnet.md)
+ [Query's uitvoeren op uw index met behulp van de python-SDK](search-get-started-python.md)
+ [Query's uitvoeren op uw index met behulp van de Java script SDK](search-get-started-javascript.md)

Aanvullende Naslag informatie over syntaxis, query architectuur en voor beelden vindt u in de volgende koppelingen:

+ [Voor beelden van Lucene-syntaxis query's voor het maken van geavanceerde query's](search-query-lucene-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Eenvoudige query syntaxis](query-simple-syntax.md)
+ [Volledige lucene-query syntaxis](query-lucene-syntax.md)
+ [Filter syntaxis](search-query-odata-filter.md)