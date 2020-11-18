---
title: Een eenvoudige query maken
titleSuffix: Azure Cognitive Search
description: Lees dit voor beeld door query's uit te voeren op basis van de eenvoudige syntaxis voor zoeken in volledige tekst, filteren op filters, geo-Zoek opdrachten, facet zoeken op basis van een Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 834e4fe8c7b3923f40a07c02c0310200db222308
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697251"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Een eenvoudige query maken in azure Cognitive Search

In azure Cognitive Search roept de [syntaxis van de eenvoudige query](query-simple-syntax.md) de standaard query-parser aan voor het uitvoeren van zoek query's in volledige tekst voor een index. Deze parser is snel en behandelt veelvoorkomende scenario's, zoals zoeken in volledige tekst, gefilterde en facetten zoeken en geo-Zoek opdrachten. 

In dit artikel gebruiken we voor beelden om de eenvoudige syntaxis te illustreren, waarbij u de `search=` para meter van een bewerking voor het [zoeken naar een document](/rest/api/searchservice/search-documents) vult.

Een alternatieve query syntaxis is [volledige lucene](query-lucene-syntax.md), waardoor complexere query structuren worden ondersteund, zoals fuzzy en zoek opdrachten met Joker tekens. Dit kan meer tijd kosten om te verwerken. Zie [de syntaxis Full lucene gebruiken](search-query-lucene-examples.md)voor meer informatie en voor beelden met de volledige syntaxis.

## <a name="formulate-requests-in-postman"></a>Aanvragen formuleren in postman

De volgende voor beelden maken gebruik van een NYC-zoek index voor taken die bestaat uit taken die beschikbaar zijn op basis van een gegevensset die wordt verschaft door de [stad New York open data](https://nycopendata.socrata.com/) Initiative. Deze gegevens mogen niet als actueel of volledig worden beschouwd. De index bevindt zich op een sandbox-service van micro soft. Dit betekent dat u geen Azure-abonnement of Azure Cognitive Search nodig hebt om deze query's uit te proberen.

Wat u nodig hebt, is postman of een gelijkwaardig hulp programma voor het uitgeven van een HTTP-aanvraag op GET. Zie voor meer informatie [Quick Start: Verken Azure Cognitive Search rest API](search-get-started-rest.md).

### <a name="set-the-request-header"></a>De aanvraag header instellen

1. Stel in de aanvraag header het **inhouds type** in op `application/json` .

2. Voeg een **API-sleutel** toe en stel deze in op deze teken reeks: `252044BE3886FE4A8E3BAA4F595114BB` . Dit is een query sleutel voor de sandbox-zoek service die als host fungeert voor de index van de NYC-taken.

Nadat u de aanvraag header hebt opgegeven, kunt u deze opnieuw gebruiken voor alle query's in dit artikel, zodat alleen de teken reeks **Search =** wordt uitgewisseld. 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Para meters ingesteld op de aanvraag header van postman" border="false":::

### <a name="set-the-request-url"></a>De aanvraag-URL instellen

Request is een GET-opdracht die wordt gekoppeld aan een URL met het Azure Cognitive Search-eind punt en de zoek teken reeks.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="OPHALEN van header van Postman-aanvraag" border="false":::

URL-samen stelling heeft de volgende elementen:

+ **`https://azs-playground.search.windows.net/`** is een zoek service in de sandbox die wordt onderhouden door het team van Azure Cognitive Search Development. 
+ **`indexes/nycjobs/`** is de index van de NYC-taken in de verzameling indexen van die service. Zowel de service naam als de index zijn vereist voor de aanvraag.
+ **`docs`** is de verzameling documenten die alle Doorzoek bare inhoud bevat. De query-API-sleutel die in de aanvraag header is gegeven, werkt alleen bij Lees bewerkingen die zijn gericht op de verzameling documenten.
+ **`api-version=2020-06-30`** Hiermee stelt u de API-versie in, een vereiste para meter voor elke aanvraag.
+ **`search=*`** is de query teken reeks, die in de eerste query null is, de eerste 50 resultaten retourneert (standaard).

## <a name="send-your-first-query"></a>Uw eerste query verzenden

Plak, als een verificatie stap, de volgende aanvraag in GET en klik op **verzenden**. Resultaten worden geretourneerd als uitgebreide JSON-documenten. Er worden volledige documenten geretourneerd, zodat u alle velden en alle waarden kunt weer geven.

Plak deze URL in een REST-client als een validatie stap en om de document structuur weer te geven.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

De query teken reeks, **`search=*`** , is een niet-opgegeven zoek opdracht die gelijk is aan Null of een lege zoek opdracht. Het is niet erg nuttig, maar dit is de eenvoudigste zoek actie die u kunt uitvoeren.

U kunt eventueel ook toevoegen **`$count=true`** aan de URL om een telling van de documenten te retour neren die overeenkomen met de zoek criteria. In een lege Zoek reeks zijn dit alle documenten in de index (ongeveer 2800 in het geval van NYC-taken).

## <a name="how-to-invoke-simple-query-parsing"></a>Eenvoudig parseren van query's aanroepen

Voor interactieve query's hoeft u niets op te geven: eenvoudig is de standaard waarde. Als u in code eerder query **type = Full** hebt aangeroepen voor de volledige query syntaxis, kunt u de standaard waarde opnieuw instellen met **query type = Simple**.

## <a name="example-1-field-scoped-query"></a>Voor beeld 1: query met veld bereik

Dit eerste voor beeld is geen parser-specifiek, maar we leiden ernaar om het eerste fundamentele query concept te introduceren: containment. In dit voor beeld wordt een query uitgevoerd op een aantal specifieke velden. Het is belang rijk dat u weet hoe u een lees bare JSON-respons structureert wanneer uw hulp programma postman of Search Explorer is. 

Voor de boog is de query alleen gericht op het *business_title* veld en geeft u op dat er alleen zakelijke titels worden geretourneerd. De syntaxis is **searchFields** om de uitvoering van query's te beperken tot alleen het business_title veld, en **Selecteer** om op te geven welke velden in het antwoord worden opgenomen.

### <a name="partial-query-string"></a>Gedeeltelijke query reeks

```http
searchFields=business_title&$select=business_title&search=*
```

Dit is dezelfde query met meerdere velden in een door komma's gescheiden lijst.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Volledige URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=business_title&$select=business_title&search=*
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Postman-voorbeeld antwoord" border="false":::

Mogelijk hebt u de zoek Score in het antwoord gezien. Een uniforme Score van 1 treedt op als er geen positie is, omdat de zoek opdracht niet in volledige tekst is gezocht, of omdat er geen criteria zijn toegepast. Voor Null-Zoek opdrachten zonder criteria worden rijen in een wille keurige volg orde weer gegeven. Wanneer u werkelijke criteria opneemt, ziet u dat zoek scores worden weer geven in betekenis volle waarden.

## <a name="example-2-look-up-by-id"></a>Voor beeld 2: opzoeken op basis van ID

Dit voor beeld is een beetje ongewoon, maar bij het evalueren van het gedrag van de zoek actie wilt u mogelijk de volledige inhoud van een specifiek document inspecteren om te begrijpen waarom het is opgenomen of uitgesloten van de resultaten. Als u één document volledig wilt retour neren, gebruikt u een [opzoek bewerking](/rest/api/searchservice/lookup-document) om de document-id door te geven.

Alle documenten hebben een unieke id. Als u de syntaxis voor een opzoek query wilt uitproberen, moet u eerst een lijst met document-Id's retour neren, zodat u er een kunt vinden om te gebruiken. Voor NYC-taken worden de id's in het veld opgeslagen `id` .

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=id&$select=id&search=*
```

Het volgende voor beeld is een opzoek query die een specifiek document retourneert `id` dat is gebaseerd op ' 9E1E3AF9-0660-4E00-AF51-9B654925A2D5 ', dat het eerst in het vorige antwoord voor komt. De volgende query retourneert het hele document, niet alleen de geselecteerde velden. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Voor beeld 3: query's filteren

De [filter syntaxis](./search-query-odata-filter.md) is een OData-expressie die u kunt gebruiken met een **Zoek opdracht** of op zichzelf. Een zelfstandig filter, zonder een zoek parameter, is handig wanneer de filter expressie volledig kan kwalificeren op interessante documenten. Zonder een query reeks is er geen lexicale of linguïstische analyse, geen Score (alle scores zijn 1) en geen classificatie. U ziet dat de zoek teken reeks leeg is.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Samen gebruikt, wordt het filter eerst op de volledige index toegepast, waarna de zoek opdracht wordt uitgevoerd op de resultaten van het filter. Filters zijn dus nuttig om de resultaten van de zoekopdracht te verbeteren, doordat het aantal documenten dat moet worden doorzocht, wordt verminderd.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Query-antwoord filteren" border="false":::

Als u dit in postman wilt proberen met behulp van GET, kunt u deze teken reeks plakken:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Een andere krachtige manier om filters en zoek opdrachten te combi neren, is via **`search.ismatch*()`** een filter expressie, waarin u een zoek query kunt gebruiken in het filter. Deze filter expressie maakt gebruik van een Joker teken op het *abonnement* om business_title te selecteren, inclusief de term plan, planner, planning, enzovoort.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Zie [Search. ismatch in ' filter voorbeelden '](./search-query-odata-full-text-search-functions.md#examples)voor meer informatie over de functie.

## <a name="example-4-range-filters"></a>Voor beeld 4: bereik filters

Bereik filtering wordt ondersteund via **`$filter`** expressies voor elk gegevens type. In de volgende voor beelden wordt gezocht naar numerieke en teken reeks velden. 

Gegevens typen zijn belang rijk in bereik filters en werken het beste als numerieke gegevens zich in numerieke velden bevinden en teken reeks gegevens in teken reeks velden. Numerieke gegevens in teken reeks velden zijn niet geschikt voor bereiken, omdat numerieke teken reeksen niet vergelijkbaar zijn in azure Cognitive Search. 

De volgende voor beelden zijn in de indeling POST voor lees baarheid (numeriek bereik, gevolgd door tekst bereik):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Bereik filter voor numerieke bereiken" border="false":::


```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Bereik filter voor tekstbereiken" border="false":::

U kunt dit ook uitproberen in postman met GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Facet overschrijding van bereik waarden is een algemene vereiste voor het zoeken van toepassingen. Zie [' filteren op basis van een bereik ' in *facet navigatie implementeren*](search-faceted-navigation.md#filter-based-on-a-range)voor meer informatie en voor beelden over het maken van filters voor facet navigatie structuren.

## <a name="example-5-geo-search"></a>Voor beeld 5: geografisch zoeken

De voor beeld-index bevat een geo_location veld met de breedte graad en lengte graad. In dit voor beeld wordt de [functie geo. Distance](./search-query-odata-geo-spatial-functions.md#examples) gebruikt waarmee wordt gefilterd op documenten in de omtrek van een begin punt, tot een wille keurige afstand (in kilo meters) die u opgeeft. U kunt de laatste waarde in de query (4) aanpassen om de surface area van de query te verkleinen of te verg Roten.

Het volgende voor beeld is in de bericht indeling voor de Lees baarheid:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Voor meer Lees bare resultaten worden Zoek resultaten afgekapt met een taak-ID, functie titel en de werk locatie. De begin coördinaten zijn verkregen van een wille keurig document in de index (in dit geval voor een werk locatie op Staten-eiland).

U kunt dit ook uitproberen in postman met GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Voor beeld 6: Zoek precisie

Term query's zijn enkele termen, misschien veel hiervan, die onafhankelijk van elkaar worden geëvalueerd. Woordgroepen query's worden tussen aanhalings tekens geplaatst en geëvalueerd als een Verbatim teken reeks. De nauw keurigheid van de overeenkomst wordt bepaald door Opera tors en Search mode.

Voor beeld 1: **`&search=fire`**  retourneert 150 resultaten, waarbij alle overeenkomsten het woord ergens in het document hebben.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire
```

Voor beeld 2: **`&search=fire department`** retourneert 2002 resultaten. Er worden overeenkomsten geretourneerd voor documenten die hetzij brand of Department bevatten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire department
```

Voor beeld 3: **`&search="fire department"`** retourneert 82 resultaten. Het sluiten van de teken reeks tussen aanhalings tekens is een Verbatim zoek opdracht op beide termen en overeenkomsten worden gevonden op basis van tokens in de index die bestaat uit de gecombineerde termen. Dit legt uit waarom een zoek actie als **`search=+fire +department`** niet gelijk is. Beide termen zijn vereist, maar worden onafhankelijk gescand. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Voor beeld 7: booleans met Search mode

Eenvoudige syntaxis ondersteunt Booleaanse Opera tors in de vorm van tekens ( `+, -, |` ). De para meter Search mode informeert de afwegingen tussen Precision en intrekken, met als voor keur `searchMode=any` intrekken (overeenkomt met een criterium dat in aanmerking komt voor een document voor de resultatenset), en om te voldoen aan de `searchMode=all` nauw keurigheid (alle criteria moeten overeenkomen). De standaard waarde is `searchMode=any` , wat verwarrend kan zijn als u een query stapelt met meerdere opera tors en brederere resultaten ophaalt. Dit is met name het geval bij niet, waarbij alle resultaten alle documenten bevatten die geen specifieke term zijn.

Met behulp van de standaard Search mode (alle) worden de 2800-documenten geretourneerd: die met de multifunctionele term "brand afdeling" en alle documenten die niet de term "Metrotech Center" hebben.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="Zoek modus any" border="false":::

Search mode wijzigen om `all` een cumulatief effect op criteria af te dwingen en een kleinere set resultaten te retour neren-21 documenten-die bestaan uit documenten met de volledige zin "brand Department", min die taken op het Metrotech Center-adres.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="Zoek modus Alles" border="false":::

## <a name="example-8-structuring-results"></a>Voor beeld 8: resultaten structureren

Verschillende para meters bepalen welke velden worden weer gegeven in de zoek resultaten, het aantal geretourneerde documenten in elke batch en de sorteer volgorde. In dit voor beeld worden enkele van de voor gaande voor beelden opnieuw weer gegeven, waarbij de resultaten worden beperkt tot specifieke velden met behulp van de **$Select** instructie en Verbatim zoek criteria, 82 overeenkomsten retour neren 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
In het vorige voor beeld kunt u sorteren op titel. Deze sortering werkt omdat civil_service_title in de index moet worden *gesorteerd* .

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Paginerings resultaten worden geïmplementeerd met behulp van de para meter **$Top** , in dit geval de vijf beste documenten retour neren:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Als u de volgende 5 wilt ophalen, slaat u de eerste batch over:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Volgende stappen
Probeer query's in uw code op te geven. In de volgende koppelingen wordt uitgelegd hoe u zoek query's instelt voor zowel .NET als de REST API met behulp van de standaard eenvoudige syntaxis.

* [Query's uitvoeren op uw index met behulp van de .NET SDK](./search-get-started-dotnet.md)
* [Query's uitvoeren op uw index met behulp van de REST API](./search-get-started-powershell.md)

Aanvullende Naslag informatie over syntaxis, query architectuur en voor beelden vindt u in de volgende koppelingen:

+ [Voor beelden van Lucene-syntaxis query's voor het maken van geavanceerde query's](search-query-lucene-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Eenvoudige query syntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Volledige lucene-query](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [De syntaxis filter en OrderBy](/rest/api/searchservice/odata-expression-syntax-for-azure-search)